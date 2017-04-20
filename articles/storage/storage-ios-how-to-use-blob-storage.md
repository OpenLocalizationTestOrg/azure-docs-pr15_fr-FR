<properties
    pageTitle="L’utilisation de stockage d’objets Blob Azure d’iOS | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec stockage d’objets Blob Azure (stockage d’objets)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>L’utilisation de stockage d’objets Blob d’iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment effectuer des scénarios courants à l’aide de stockage d’objets Blob Microsoft Azure. Les exemples sont écrits en objectif C et utilisent de la [Bibliothèque de Client de stockage Azure pour iOS](https://github.com/Azure/azure-storage-ios). Les scénarios présentés incluent **Télécharger**, **liste**, **le téléchargement**et **suppression** des objets BLOB. Pour plus d’informations sur les objets BLOB, consultez la section [Étapes suivantes](#next-steps) . Vous pouvez également télécharger l' [exemple d’application](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) pour visualiser rapidement l’utilisation du stockage Azure dans une application iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importer la bibliothèque d’iOS stockage Azure dans votre application

Vous pouvez importer la bibliothèque iOS stockage Azure dans votre application à l’aide de la [CocoaPod de stockage Azure](https://cocoapods.org/pods/AZSClient) ou en important le fichier **Framework** .

## <a name="cocoapod"></a>CocoaPod

1. Si vous n’avez pas déjà fait, [Installez CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) sur votre ordinateur en ouvrant une fenêtre de terminal et en exécutant la commande suivante

        sudo gem install cocoapods

2. Ensuite, dans le répertoire du projet (le répertoire contenant votre `.xcodeproj` fichier), créez un nouveau fichier appelé `Podfile`(sans extension de fichier). Ajoutez le code suivant à `Podfile` et enregistrer

        pod 'AZSClient'

3. Dans la fenêtre du terminal, accédez au répertoire du projet et exécutez la commande suivante

        pod install

4. Si votre `.xcodeproj` est ouvert dans Xcode, fermez-le. Dans le répertoire de votre projet, ouvrez le fichier de projet nouvellement créé qui aura la `.xcworkspace` extension. C’est le fichier que vous allez utiliser à partir de maintenant.

## <a name="framework"></a>Cadre
Pour pouvoir utiliser la bibliothèque iOS stockage Azure, vous devez d’abord créer le fichier framework.

1. Tout d’abord, téléchargez ou cloner l' [mis en pension azure-stockage-ios](https://github.com/azure/azure-storage-ios).

2. Accédez à *azure-stockage-ios* -> *bibliothèque* -> *Azure stockage Client bibliothèque*et ouvrir `AZSClient.xcodeproj` dans Xcode.

3. Dans le coin supérieur gauche de Xcode, modifier le jeu actif à partir de « Azure stockage Client bibliothèque » à « Infrastructure ».

4. Générez le projet (⌘ + B). Cela créera un `AZSClient.framework` fichier sur votre bureau.

Vous pouvez ensuite importer le fichier framework dans votre application en procédant comme suit :

1. Créez un nouveau projet ou ouvrez votre projet existant dans Xcode.

2. Cliquez sur votre projet dans le volet de navigation gauche, cliquez sur l’onglet *Général* en haut de l’Éditeur du projet.

3. Sous la section *structures liées et des bibliothèques* , cliquez sur le bouton Ajouter (+).

4. Cliquez sur *ajouter d’autres...*. Accédez au et ajoutez la `AZSClient.framework` fichier que vous venez de créer.

5. Sous la section *structures liées et des bibliothèques* , cliquez à nouveau sur le bouton Ajouter (+).

6. Dans la liste des bibliothèques déjà fournies, recherchez `libxml2.2.dylib` et l’ajouter à votre projet.

7. Cliquez sur l’onglet *Paramètres de génération* en haut de l’Éditeur du projet.

8. Sous la section *Chemins d’accès de la recherche* , double-cliquez sur *Les chemins de recherche Framework* et ajoutez le chemin d’accès à votre `AZSClient.framework` fichier.

## <a name="import-statement"></a>Instruction d’importation
Vous devrez inclure l’instruction import suivante dans le fichier dans lequel vous voulez appeler l’API de stockage Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Opérations asynchrones
> [AZURE.NOTE] Toutes les méthodes qui effectuent une requête auprès du service sont opérations asynchrones. Dans les exemples de code, vous trouverez que ces méthodes ont un gestionnaire d’achèvement. Code à l’intérieur du Gestionnaire d’achèvement s’exécute **une fois** que la demande est terminée. Code une fois que le Gestionnaire d’achèvement s’exécutera **alors que** la demande est effectuée.

## <a name="create-a-container"></a>Créer un conteneur
Chaque blob dans le stockage Azure doit se trouver dans un conteneur. L’exemple suivant montre comment créer un conteneur, appelé *newcontainer*, dans votre compte de stockage s’il n’existe pas. Lors du choix d’un nom pour votre conteneur, être gardant à l’esprit les règles d’appellation présentés ci-dessus.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

Vous pouvez vérifier que cela fonctionne en consultant l' [Explorateur de stockage de Microsoft Azure](http://storageexplorer.com) et en vérifiant que *newcontainer* se trouve dans la liste des conteneurs de votre compte de stockage.

## <a name="set-container-permissions"></a>Définir des autorisations de conteneur
Autorisations d’un conteneur sont configurées pour l’accès **privé** par défaut. Toutefois, les conteneurs fournissent quelques différentes options pour l’accès de conteneur :

- **Privé**: données conteneur et blob peuvent être lus par le propriétaire du compte uniquement.

- **Objets BLOB**: données Blob dans ce conteneur peuvent être lus par le biais demande anonyme, mais les données de conteneur ne seront pas disponibles. Clients Impossible d’énumérer les objets BLOB dans le conteneur via demande anonyme.

- **Conteneur**: données conteneur et blob peuvent être lus par le biais demande anonyme. Les clients peuvent énumérer les objets BLOB dans le conteneur via demande anonyme, mais Impossible d’énumérer les conteneurs à l’intérieur du compte de stockage.

L’exemple suivant vous montre comment créer un conteneur avec les autorisations d’accès **conteneur** qui autoriseront l’accès public et en lecture seule pour tous les utilisateurs sur Internet :

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur
Comme indiqué dans la section [concepts de service Blob](#blob-service-concepts) , stockage d’objets Blob propose trois différents types d’objets BLOB : bloquer des objets BLOB, ajouter des objets BLOB et des objets BLOB de page. Pour l’instant, la bibliothèque iOS stockage Azure prend uniquement en charge des objets BLOB bloc. Dans la plupart des cas, blob bloc est le type recommandé à utiliser.

L’exemple suivant montre comment télécharger un blob bloc à partir d’un NSString. Si un blob portant le même nom existe déjà dans ce conteneur, le contenu de ce blob est remplacé.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

Vous pouvez vérifier que cela fonctionne en consultant l' [Explorateur de stockage de Microsoft Azure](http://storageexplorer.com) et en vérifiant que le conteneur, *containerpublic*, contienne le blob, *sampleblob*. Dans cet exemple, nous avons utilisé un conteneur public afin que vous pouvez également vérifier que cela a fonctionné en accédant aux objets BLOB URI :

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

En plus de télécharger un blob bloc à partir d’un NSString, existe des méthodes similaires pour NSData, NSInputStream ou un fichier local.

## <a name="list-the-blobs-in-a-container"></a>Répertorier les objets BLOB dans un conteneur
L’exemple suivant montre comment répertorier tous les objets BLOB dans un conteneur. Lorsque vous effectuez cette opération, être gardant à l’esprit les paramètres suivants :     

- **continuationToken** - le jeton de continuation représente l’endroit où l’opération de la liste doit commencer. Si aucun jeton est fourni, il permet de répertorier des objets BLOB à partir du début. N’importe quel nombre d’objets BLOB peut être répertorié, à partir de zéro jusqu'à un maximum spécifié. Même si cette méthode retourne zéro des résultats, si `results.continuationToken` n’est pas égaux à zéro, il peut y avoir plusieurs objets BLOB sur le service qui n’ont pas été répertoriés.
- **préfixe** - vous pouvez spécifier le préfixe à utiliser pour la liste objets blob. Uniquement les objets BLOB qui commencent par ce préfixe apparaît.
- **useFlatBlobListing** - comme indiqué dans la section [noms et référencement des conteneurs et des objets BLOB](#naming-and-referencing-containers-and-blobs) , bien que le service Blob est un jeu de stockage plate, vous pouvez créer une hiérarchie virtuelle en nommant BLOB avec les informations de chemin d’accès. Toutefois, la liste non plate est actuellement pas pris en charge ; Cela sera bientôt disponible. Pour l’instant, cette valeur doit être`YES`
- **blobListingDetails** - vous pouvez spécifier les éléments à inclure lors de l’affichage des objets BLOB
    - `AZSBlobListingDetailsNone`: Répertorier uniquement des objets BLOB validées et ne retournent pas de métadonnées d’objets blob.
    - `AZSBlobListingDetailsSnapshots`: Liste validée des objets BLOB et les objets blob instantanés.
    - `AZSBlobListingDetailsMetadata`: Récupérer blob métadonnées pour chaque blob renvoyés dans la liste.
    - `AZSBlobListingDetailsUncommittedBlobs`: La liste des objets BLOB validées et non validées.
    - `AZSBlobListingDetailsCopy`: Inclure des propriétés de la copie dans la liste.
    - `AZSBlobListingDetailsAll`: Répertorier tous les objets BLOB validées disponibles, des objets BLOB non validées et des instantanés et renvoyer tous les métadonnées et copier l’état de ces objets BLOB.
- **maxResults** - le nombre maximal de résultats à renvoyer pour cette opération. Utilisez -1 pour ne pas défini une limite.
- **completionHandler** - le bloc de code à exécuter avec les résultats de l’opération de la liste.

Dans cet exemple, une méthode d’assistance est utilisée pour appeler de manière récursive la liste objets BLOB méthode chaque fois qu’un jeton de continuation est renvoyé.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>Télécharger un blob

L’exemple suivant montre comment télécharger un blob à un objet NSString.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>Supprimer un blob

L’exemple suivant montre comment supprimer un blob.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>Supprimer un conteneur blob

L’exemple suivant montre comment supprimer un conteneur.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à utiliser le stockage Blob d’iOS, suivez ces liens pour en savoir plus sur la bibliothèque d’iOS et le service de stockage.

- [Bibliothèque de Client stockage Azure pour iOS](https://github.com/azure/azure-storage-ios)
- [IOS stockage Azure Documentation de référence](http://azure.github.io/azure-storage-ios/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage)

Si vous avez des questions concernant cette bibliothèque n’hésitez pas à publier des billets dans notre [forum MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou [Débordement de pile](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Si vous avez des suggestions de fonctionnalité pour le stockage Azure, publiez [Azure stockage commentaires](https://feedback.azure.com/forums/217298-storage/).
