<properties
 pageTitle="Importer exporter des identités des appareils IoT concentrateur | Microsoft Azure"
 description="Concepts et .NET extraits pour la gestion en bloc des identités des appareils IoT concentrateur de code"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>En bloc de gestion des identités d’appareil IoT concentrateur

Chaque concentrateur IoT comporte un Registre identité appareil que vous pouvez utiliser pour créer des ressources par périphérique dans le service, par exemple une file d’attente contenant les messages cloud vers le périphérique en cours d’exécution. Le Registre d’identité de périphérique permet également contrôler l’accès aux points de terminaison not appareil. Cet article décrit comment importer et exporter des identités appareil en bloc à partir d’un appareil identité de Registre.

Importer et exporter des opérations ont lieu dans le cadre des *tâches* qui vous permettent d’exécuter des opérations de service en bloc par rapport à un concentrateur IoT.

La classe **RegistryManager** inclut les **ExportDevicesAsync** et les méthodes **ImportDevicesAsync** qui utilisent l’infrastructure de **travail** . Ces méthodes permettent à exporter, importer et synchroniser l’intégralité d’un registre d’appareil IoT concentrateur.

## <a name="what-are-jobs"></a>Que sont les tâches ?

Opérations de Registre appareil identité utilisent le système de **travail** lorsque l’opération :

*  A potentiellement beaucoup de temps d’exécution par rapport aux opérations runtime standard, ou
*  Renvoie une grande quantité de données à l’utilisateur.

Dans ce cas, au lieu d’un seul appel d’API en attente ou blocage du résultat de l’opération, l’opération crée asynchrone une **tâche** de ce concentrateur IoT. L’opération puis renvoie immédiatement un objet **JobProperties** .

L’extrait de code c# suivant montre comment créer une tâche d’exportation :

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Ensuite, vous pouvez utiliser la classe **RegistryManager** pour demander l’état de la **tâche** à l’aide de métadonnées **JobProperties** retournées.

L’extrait de code c# suivant montre comment interroger toutes les cinq secondes pour voir si la tâche est terminée :

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Exporter des appareils mobiles

Utilisez la méthode **ExportDevicesAsync** exporter l’intégralité d’un registre d’appareil IoT concentrateur dans un conteneur blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à l’aide d’une [Signature d’accès partagé](https://msdn.microsoft.com/library/ee395415.aspx).

Cette méthode vous permet de créer des sauvegardes fiables des informations de votre appareil dans un conteneur blob que vous contrôlez.

La méthode **ExportDevicesAsync** requiert deux paramètres :

*  Une *chaîne* qui contient un URI d’un conteneur blob. Cet URI doit contenir un jeton associations de sécurité qui accorde l’accès en écriture au conteneur. La tâche crée un blob bloc dans ce conteneur pour stocker les données de périphérique série exporter. Le jeton de sa doit inclure les autorisations suivantes :
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *Booléen* qui indique si vous voulez exclure des clés d’authentification de votre exporter les données. Si la sortie **false**, l’authentification clés sont inclus dans la zone export ; dans le cas contraire, les touches sont exportés sous forme de **null**.

L’extrait de code c# suivant montre comment commencer une opération d’exportation incluant des clés d’authentification appareil dans les données exportées et puis interroger fin :

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

La tâche stocke sa sortie dans le conteneur blob fourni sous forme d’un blob bloc avec le nom **devices.txt**. Les données de sortie se composent de données de périphérique JSON sérialisé, avec une seule unité par ligne.

L’exemple suivant montre les données de sortie :

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Si vous devez accéder à ces données dans le code, vous pouvez facilement désérialiser ces données à l’aide de la classe **ExportImportDevice** . L’extrait de code c# suivant montre comment lire des informations sur le périphérique qui ont été précédemment exportées vers un blob bloc :

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  Vous pouvez également utiliser la méthode **GetDevicesAsync** de la classe **RegistryManager** pour récupérer une liste de vos appareils. Toutefois, cette approche a une lettrine difficile de 1000 sur le nombre d’objets appareil qui sont retournés. Le cas d’utilisation prévu pour la méthode **GetDevicesAsync** concerne les scénarios de développement faciliter le débogage et n’est pas recommandé pour les charges de travail.

## <a name="import-devices"></a>Périphériques d’importation

La méthode **ImportDevicesAsync** de la classe **RegistryManager** permet de vous permet d’effectuer les opérations d’importation et synchronisation en bloc dans un Registre IoT concentrateur appareil. Comme la méthode **ExportDevicesAsync** , la méthode **ImportDevicesAsync** utilise le cadre de la **tâche** .

Veillez à l’aide de la méthode **ImportDevicesAsync** , car en plus de la configuration de nouveaux appareils dans le Registre identité appareil, il peut également mettre à jour et supprimer des périphériques existants.

> [AZURE.WARNING]  Une opération d’importation ne peut pas être annulée. Sauvegardez toujours vos données existantes à l’aide de la méthode **ExportDevicesAsync** vers un autre conteneur blob avant d’apporter des modifications en bloc à votre Registre identité appareil.

La méthode **ImportDevicesAsync** prend deux paramètres :

*  *Chaîne* qui contient l’URI d’un conteneur d’objets blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à utiliser comme *entrée* pour la tâche. Cet URI doit contenir un jeton associations de sécurité qui accorde l’accès en lecture au conteneur. Ce conteneur doit contenir un blob avec le nom **devices.txt** qui contient les données de série appareil à importer dans le Registre identité appareil. Les données à importer doivent contenir des informations sur le périphérique dans le même format JSON que la tâche **ExportImportDevice** utilise lorsqu’il crée un blob **devices.txt** . Le jeton de sa doit inclure les autorisations suivantes :

    ```
    SharedAccessBlobPermissions.Read
    ```

*  *Chaîne* qui contient l’URI d’un conteneur d’objets blob [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) à utiliser comme *résultat* de la tâche. La tâche crée un blob bloc dans ce conteneur pour stocker les informations d’erreur à partir de l’importation **travail**. Le jeton de sa doit inclure les autorisations suivantes :
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Les deux paramètres peuvent pointer vers le même conteneur blob. Les paramètres distincts il suffit d’activer davantage de contrôle sur vos données comme le conteneur de sortie nécessite des autorisations supplémentaires.

L’extrait de code c# suivant montre comment lancer une tâche d’importation :

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>Comportement de l’importation

Vous pouvez utiliser la méthode **ImportDevicesAsync** pour effectuer les opérations suivantes en bloc dans le Registre identité appareil :

-   Inscription en bloc des nouveaux appareils
-   Suppressions en bloc des périphériques existants
-   En bloc des changements d’état (activer ou désactiver des périphériques)
-   Affectation en bloc des nouvelles clés d’authentification appareil
-   Automatique-reconstruction en bloc des clés d’authentification appareil

Vous pouvez effectuer n’importe quelle combinaison des opérations au sein d’un seul appel **ImportDevicesAsync** précédentes. Par exemple, vous pouvez enregistrer les nouveaux appareils et supprimer ou mettre à jour les périphériques existants en même temps. Lorsqu’il est utilisé avec la méthode **ExportDevicesAsync** , vous pouvez migrer complètement tous vos appareils à partir d’un concentrateur IoT à l’autre.

Utilisez la propriété facultative **importMode** dans les données de sérialisation importer pour chaque appareil pour contrôler le processus par-périphérique d’importation. La propriété **importMode** comporte les options suivantes :

| importMode |  Description |
| -------- | ----------- |
| **createOrUpdate** | Si un appareil n’existe pas avec l' **id**spécifié, il est enregistré récemment. <br/>Si le périphérique existe déjà, informations existantes sont remplacées par les données d’entrée fournies sans tenir compte de la valeur **ETag** . |
| **créer** | Si un appareil n’existe pas avec l' **id**spécifié, il est enregistré récemment. <br/>Si le périphérique existe déjà, une erreur est enregistrée dans le fichier journal. |
| **mise à jour** | Si un périphérique existe déjà avec l' **id**spécifié, informations existantes sont remplacées par les données d’entrée fournies sans tenir compte de la valeur **ETag** . <br/>Si le périphérique n’existe pas, une erreur est enregistrée dans le fichier journal. |
| **updateIfMatchETag** | Si un périphérique existe déjà avec l' **id**spécifié, informations existantes sont remplacées par les données d’entrée fournies uniquement s’il existe une correspondance **ETag** . <br/>Si le périphérique n’existe pas, une erreur est enregistrée dans le fichier journal. <br/>S’il existe une incompatibilité **ETag** , une erreur est enregistrée dans le fichier journal. |
| **createOrUpdateIfMatchETag** | Si un appareil n’existe pas avec l' **id**spécifié, il est enregistré récemment. <br/>Si le périphérique existe déjà, informations existantes sont remplacées par les données d’entrée fournies uniquement s’il existe une correspondance **ETag** . <br/>S’il existe une incompatibilité **ETag** , une erreur est enregistrée dans le fichier journal. |
| **supprimer** | Si un périphérique existe déjà avec l' **id**spécifié, celui-ci est supprimé quelle que soit la valeur **ETag** . <br/>Si le périphérique n’existe pas, une erreur est enregistrée dans le fichier journal. |
| **deleteIfMatchETag** | Si un périphérique existe déjà avec l' **id**spécifié, celui-ci est supprimé uniquement s’il existe une correspondance **ETag** . Si le périphérique n’existe pas, une erreur est enregistrée dans le fichier journal. <br/>S’il existe une incompatibilité ETag, une erreur est enregistrée dans le fichier journal. |

> [AZURE.NOTE] Si les données de sérialisation ne définissent pas explicitement un indicateur **importMode** pour un appareil, par défaut **createOrUpdate** pendant l’opération d’importation.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importer exemple appareils – en bloc appareil mise en service 

L’exemple de code c# suivant illustre comment générer plusieurs identités appareil qui :

- Inclure des clés d’authentification.
- Écrire ces informations appareil dans un blob bloc.
- Importer les appareils dans le Registre identité appareil.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemple de périphériques importer – suppression en bloc

L’exemple de code suivant montre comment supprimer les appareils que vous avez ajouté à l’aide de l’exemple précédent :

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>Obtenir le conteneur SAS URI


L’exemple de code suivant vous montre comment générer un [URI associations de sécurité](../storage/storage-dotnet-shared-access-signature-part-2.md) avec lecture, écriture et supprimer les autorisations pour un conteneur blob :

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment effectuer des opérations en bloc sur le Registre d’identité de périphérique dans un concentrateur IoT. Suivez ces liens pour en savoir plus sur la gestion des Azure IoT concentrateur :

- [Mesures de l’utilisation][lnk-metrics]
- [Contrôle des opérations][lnk-monitor]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md