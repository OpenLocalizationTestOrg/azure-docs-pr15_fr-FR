<properties 
    pageTitle="Télécharger des fichiers dans un compte Media Services à l’aide de .NET | Microsoft Azure" 
    description="Découvrez comment intégrer du contenu multimédia à Media Services en créant et en téléchargement actifs." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>Télécharger des fichiers dans un compte Media Services à l’aide de .NET

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTE](media-services-rest-upload-files.md)
 - [Portail](media-services-portal-upload-files.md)

Dans les Services de support, vous téléchargez (ou acquisition) vos fichiers numériques dans un bien. L’entité de **biens** peut contenir vidéo, audio, images, collections miniatures, texte pistes et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés, votre contenu est stocké en toute sécurité dans le nuage pour un traitement ultérieur et la diffusion en continu.

Les fichiers dans l’actif sont appelés **Fichiers actif**. L’instance **AssetFile** et le fichier multimédia réel sont deux objets distincts. L’instance AssetFile contient des métadonnées sur le fichier multimédia, tandis que le fichier multimédia contient le contenu du média réel.

>[AZURE.NOTE]Les considérations suivantes s’appliquent lors du choix d’un nom de fichier actif :
>
>- Media Services utilise la valeur de la propriété IAssetFile.Name lors de la création d’URL pour le contenu de la diffusion en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Pour cette raison, encodage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères pour cent-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il peut uniquement être une «. » pour l’extension de nom de fichier.
>
>- La longueur du nom ne doit pas dépasser 260 caractères.

Lorsque vous créez des éléments, vous pouvez spécifier les options de chiffrement suivantes. 

- **Aucun** : aucun chiffrement est utilisé. Il s’agit de la valeur par défaut. Notez que lorsque vous utilisez cette option votre contenu n’est pas protégé lors des transferts ou au reste dans le stockage.
Si vous envisagez d’effectuer un MP4 à l’aide de téléchargement progressif, utilisez cette option. 
- **CommonEncryption** - Utilisez cette option si vous téléchargez du contenu déjà chiffré et protégé par chiffrement courantes ou PlayReady DRM (par exemple, Smooth Streaming protégé avec PlayReady DRM).
- **EnvelopeEncrypted** – Utilisez cette option si vous téléchargez TLS cryptée avec AES. Notez que les fichiers doivent avoir été codés et chiffrés par gestionnaire transformer.
- **StorageEncrypted** - chiffre votre contenu effacer localement en utilisant le chiffrement AES-256 bits, puis chargements à l’endroit où il est stocké le stockage Azure chiffré au reste. Ressources protégés par le chiffrement de stockage sont automatiquement non chiffrés et placés dans un système de fichiers chiffrés avant le codage et éventuellement chiffrés avant de le télécharger comme un nouvel actif de sortie. Le cas d’utilisation principal pour le stockage de chiffrement est lorsque vous voulez protéger vos fichiers de média d’entrée de haute qualité avec chiffrement fort inactives sur disque.

    Media Services fournit chiffrement de stockage sur disque de vos ressources, pas sur la transmission tels que le Gestionnaire de droits numériques (DRM).

    Si votre actif est stockage chiffré, vous devez configurer stratégie de remise de biens. Pour plus d’informations, voir [stratégie de remise de biens de configuration](media-services-dotnet-configure-asset-delivery-policy.md).

Si vous spécifiez pour vos biens à être chiffrés avec une option de **CommonEncrypted** , ou une option **EnvelopeEncypted** , vous devrez associer votre actif un **ContentKey**. Pour plus d’informations, voir [comment créer un ContentKey](media-services-dotnet-create-contentkey.md). 

Si vous spécifiez pour vos biens à être chiffrés avec une option de **StorageEncrypted** , le Kit de développement de Services de support pour .NET créera un **StorateEncrypted** **ContentKey** pour votre actif.


Cette rubrique indique comment utiliser Media Services .NET SDK ainsi que les extensions Media Services .NET SDK pour télécharger des fichiers dans un actif Media Services.

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Télécharger un fichier unique avec Media Services .NET SDK 

L’exemple de code ci-dessous utilise .NET SDK pour effectuer les tâches suivantes : 

- Crée un bien vide.
- Crée une instance de AssetFile que nous souhaitons associer à l’actif.
- Crée une instance AccessPolicy qui définit les autorisations et la durée d’accès à la ressource.
- Crée une instance Locator qui permet d’accéder à la ressource.
- Télécharge un seul fichier multimédia dans Media Services. 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>Télécharger plusieurs fichiers avec Media Services .NET SDK 

Le code suivant montre comment créer un bien et télécharger plusieurs fichiers.

Le code effectue les opérations suivantes :
    
-   Crée un bien vide à l’aide de la méthode CreateEmptyAsset définie dans l’étape précédente.
    
-   Crée une instance **AccessPolicy** qui définit les autorisations et la durée d’accès à la ressource.
    
-   Crée une instance **Locator** qui permet d’accéder à la ressource.
    
-   Crée une instance de **BlobTransferClient** . Ce type représente un client qui fonctionne sur les objets BLOB Azure. Dans cet exemple, nous utilisons le client pour contrôler la progression du téléchargement. 
    
-   Énumère les fichiers dans le répertoire spécifié et crée une instance **AssetFile** pour chaque fichier.
    
-   Effet de télécharger les fichiers dans Media Services à l’aide de la méthode **UploadAsync** . 
    
>[AZURE.NOTE] Utilisez la méthode UploadAsync pour vous assurer que les appels ne bloquent pas et les fichiers sont téléchargés en parallèle.
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Lorsque vous transférez un grand nombre de biens, posez-vous les questions suivantes.

- Créer un nouvel objet **CloudMediaContext** par thread. La classe **CloudMediaContext** n’est pas thread safe.
 
- Augmenter NumberOfConcurrentTransfers de la valeur par défaut de 2 sur une valeur supérieure à 5. Définition de cette propriété affecte toutes les instances de **CloudMediaContext**. 
 
- Conservez ParallelTransferThreadCount la valeur par défaut de 10.
 
##<a id="ingest_in_bulk"></a>Traiter les éléments en bloc à l’aide de Media Services .NET SDK 

Téléchargement de fichiers volumineux biens peut être une critique lors de la création de biens. Traiter les biens dans en bloc ou « En bloc traiter », implique découplage la création de biens à partir du processus de téléchargement. Pour utiliser un bloc traiter les approche, créez un manifeste (IngestManifest) qui décrit l’actif et les fichiers associés. Puis utilisez la méthode de téléchargement de votre choix pour télécharger les fichiers associés au conteneur de blob du manifeste. Microsoft Azure Media Services surveille le conteneur blob associé au manifeste. Une fois qu’un fichier est téléchargé dans le conteneur blob, Microsoft Azure Media Services complète la création de biens basée sur la configuration de l’actif dans le manifeste (IngestManifestAsset).


Pour créer un nouvel appel IngestManifest la méthode Create exposée par la collection IngestManifests sur la CloudMediaContext. Cette méthode crée un nouveau IngestManifest avec le nom de manifeste que vous avez défini.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Créer les ressources qui seront associés à la plus grande partie IngestManifest. Configurer les options de chiffrement souhaité de l’actif pour traiter les en bloc.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Un IngestManifestAsset associe un bien à un IngestManifest en bloc pour traiter les en bloc. Il associe également AssetFiles qui composent chaque actif. Pour créer un IngestManifestAsset, utilisez la méthode Create dans le contexte de serveur.

L’exemple suivant illustre l’ajout IngestManifestAssets nouveau deux qu’associer les deux biens précédemment créées pour l’essentiel d’acquisition manifeste. Chaque IngestManifestAsset associe également un jeu de fichiers à télécharger pour chaque ressource au cours de traiter les en bloc.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
Vous pouvez utiliser n’importe quelle application de client haut débit capable de télécharger les fichiers de biens pour le conteneur de stockage blob URI fourni par la propriété **IIngestManifest.BlobStorageUriForUpload** de la IngestManifest. Un service de téléchargement remarquables haut débit est [Aspera sur la demande d’Application Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Vous pouvez également écrire du code pour télécharger les fichiers de biens, comme le montre l’exemple suivant.
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

Le code pour télécharger les fichiers de biens pour l’échantillon utilisé dans cette rubrique est indiqué dans l’exemple suivant.
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

Vous pouvez déterminer l’avancement de la réception en bloc pour toutes les ressources associées avec un **IngestManifest** par l’interrogation de la propriété statistiques de **IngestManifest**. Pour mettre à jour les informations d’avancement, vous devez utiliser un nouveau **CloudMediaContext** chaque fois que vous interrogez la propriété statistiques.

L’exemple suivant montre un IngestManifest par son **Id**de l’interrogation.
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>Télécharger des fichiers à l’aide des Extensions SDK .NET 

L’exemple ci-dessous montre comment télécharger un fichier unique à l’aide de .NET SDK Extensions. Dans ce cas la méthode **CreateFromFile** est utilisée, mais la version asynchrone est également disponible (**CreateFromFileAsync**). La méthode **CreateFromFile** vous permet de spécifier le nom du fichier, option de chiffrement et un rappel afin d’indiquer la progression du téléchargement du fichier.


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

L’exemple suivant appelle fonction UploadFile et spécifie le chiffrement de stockage en tant que l’option de création de biens.  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>Étape suivante

À présent que vous avez téléchargé un bien aux Services de support, consultez la rubrique [comment obtenir un processeur média][] .

[Comment obtenir un processeur média]: media-services-get-media-processor.md
 
