<properties
    pageTitle="Comment utiliser le stockage de fichiers à partir de C++ | Microsoft Azure"
    description="Stocker les données du fichier dans le nuage avec le stockage de fichiers Azure."
    services="storage"
    documentationCenter=".net"
    authors="seguler"
    manager="jahogg"
    editor="tysonn" />

<tags ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="seguler" />

# <a name="how-to-use-file-storage-from-c"></a>Comment utiliser le stockage de fichiers à partir de C++

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]
[AZURE.INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>À propos de ce didacticiel

Dans ce didacticiel, vous allez découvrir comment effectuer des opérations de base sur le service de stockage Microsoft Azure. Par le biais exemples écrits en C++, vous allez apprendre à créer des actions et des répertoires, charger, de liste et supprimer des fichiers. Si vous débutez au service de stockage de fichiers de Microsoft Azure, passant par les concepts dans les sections suivantes sera très utile pour comprendre les exemples.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Créer une application C++

Pour générer les exemples, vous avez besoin installer la bibliothèque de Client de stockage Azure 2.4.0 pour C++. Vous devez également créer un compte de stockage Azure.

Pour installer le Client de stockage Azure 2.4.0 pour C++, vous pouvez utiliser une des méthodes suivantes :

-   **Linux :** Suivez les instructions fournies dans la page de [Bibliothèque de Client de stockage Azure pour C++ Lisezmoi](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .

-   **Windows :** Dans Visual Studio, cliquez sur **outils &gt; Gestionnaire de Package NuGet &gt; Console du Gestionnaire de Package**. Tapez la commande suivante dans la [console Gestionnaire de Package NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) , appuyez sur **entrée**.

    Package d’installation wastorage

## <a name="set-up-your-application-to-use-file-storage"></a>Configurer votre application pour utiliser le stockage de fichiers

Ajoutez que les instructions vers le haut du fichier C++ où vous souhaitez utiliser le stockage Azure API pour accéder aux fichiers include suivantes :

    #include "was/storage_account.h"
    #include "was/file.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurer une chaîne de connexion de stockage Azure

Pour utiliser le stockage de fichiers, vous devez vous connecter à votre compte de stockage Azure. La première étape serait pour configurer une chaîne de connexion qui nous utiliserons pour vous connecter à votre compte de stockage. Nous allons définir une variable statique pour ce faire.

    // Define the connection-string with your values.
    const utility::string_t 
    storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

## <a name="connecting-to-an-azure-storage-account"></a>Connexion à un compte de stockage Azure

Vous pouvez utiliser la classe **cloud_storage_account** pour représenter vos informations de compte de stockage. Pour récupérer vos informations de compte de stockage de la chaîne de connexion de stockage, vous pouvez utiliser la méthode **analyser** .

    // Retrieve storage account from connection string. 
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-share"></a>Comment : créer un partage

Tous les fichiers et des répertoires de stockage de fichiers se trouvent dans un conteneur appelé un **partage**. Votre compte de stockage peut avoir partages autant que permet la capacité de votre compte. Pour obtenir l’accès à un partage de sites et son contenu, vous devez utiliser un client de stockage de fichier.

    // Create the file storage client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();

Utilise le client de stockage de fichier, vous pouvez ensuite obtenir une référence à un partage de.

    // Get a reference to the file share
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));

Pour créer le partage, utilisez la méthode **create_if_not_exists** de l’objet **cloud_file_share** .

    if (share.create_if_not_exists()) { 
        std::wcout << U("New share created") << std::endl;  
    }

À ce stade, **partager** contient une référence à un partage nommé **mon exemple-partager**.

## <a name="how-to-upload-a-file"></a>Comment : télécharger un fichier

Au minimum, un partage de stockage de fichiers Azure contient un répertoire racine dans lequel les fichiers peuvent résider. Dans cette section, vous allez découvrir comment télécharger un fichier à partir du stockage local vers le répertoire racine d’un partage.

La première étape de téléchargement d’un fichier est d’obtenir une référence à l’annuaire dans lequel il doit se trouver. Pour cela, en appelant la méthode **get_root_directory_reference** de l’objet partage.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();

Maintenant que vous avez une référence à la racine du partage, vous pouvez télécharger un fichier sur ce dernier. Cet exemple télécharge à partir d’un flux de données à partir d’un fichier et du texte.

    // Upload a file from a stream.
    concurrency::streams::istream input_stream = 
      concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

    azure::storage::cloud_file file1 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
    file1.upload_from_stream(input_stream);

    // Upload some files from text.
    azure::storage::cloud_file file2 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    file2.upload_text(_XPLATSTR("more text"));

    // Upload a file from a file.
    azure::storage::cloud_file file4 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    file4.upload_from_file(_XPLATSTR("DataFile.txt"));  

## <a name="how-to-create-a-directory"></a>Comment : créer un répertoire

Vous pouvez également organiser le stockage en plaçant les fichiers à l’intérieur de sous-répertoires plutôt que de les laisser dans le répertoire racine. Le service de stockage de fichiers Azure permet de créer autant de répertoires que permettra à votre compte. Le code suivant crée un répertoire nommé **répertoire mes exemples** sous le répertoire racine, ainsi que les un sous-répertoire nommé **sous-répertoire mon exemple**.
    
    // Retrieve a reference to a directory
    azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Return value is true if the share did not exist and was successfully created.
    directory.create_if_not_exists();
    
    // Create a subdirectory.
    azure::storage::cloud_file_directory subdirectory = 
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    subdirectory.create_if_not_exists();

## <a name="how-to-list-files-and-directories-in-a-share"></a>Comment : la liste des fichiers et des répertoires dans un partage

Obtention d’une liste de fichiers et des répertoires au sein d’un partage facilement exécuté en appelant **list_files_and_directories** sur une référence **cloud_file_directory** . Pour accéder à l’ensemble complet de propriétés et les méthodes pour un retourné **list_file_and_directory_item**, vous devez appeler la méthode **list_file_and_directory_item.as_file** pour obtenir un objet **cloud_file** ou la méthode **list_file_and_directory_item.as_directory** pour obtenir un objet **cloud_file_directory** .

Le code suivant montre comment récupérer et l’URI de chaque élément dans le répertoire racine du partage de sortie.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    // Output URI of each item.
    azure::storage::list_file_and_diretory_result_iterator end_of_results;
    
    for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
    {
        if(it->is_directory())
        {
            ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
        else if (it->is_file())
        {
            ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
        }       
    }
    

## <a name="how-to-download-a-file"></a>Comment : télécharger un fichier

Pour télécharger des fichiers, tout d’abord extraire une référence de fichier, puis appelez la méthode **download_to_stream** pour transférer le contenu du fichier à un objet de flux de données qui vous pouvez conserver puis vers un fichier local. Par ailleurs, vous pouvez utiliser la méthode **download_to_file** pour télécharger le contenu d’un fichier vers un fichier local. Vous pouvez utiliser la méthode **download_text** pour télécharger le contenu d’un fichier comme étant une chaîne de texte.

L’exemple suivant utilise les méthodes **download_to_stream** et **download_text** pour illustrer télécharger les fichiers qui ont été créés dans les sections précédentes.

    // Download as text
    azure::storage::cloud_file text_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    utility::string_t text = text_file.download_text();
    ucout << "File Text: " << text << std::endl;
    
    // Download as a stream.
    azure::storage::cloud_file stream_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    stream_file.download_to_stream(output_stream);
    std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();

## <a name="how-to-delete-a-file"></a>Comment : supprimer un fichier

Une autre opération de stockage de fichier courantes est la suppression du fichier. Le code suivant supprime un fichier nommé mon-exemple-fichier-3 stockées sous le répertoire racine.

    // Get a reference to the root directory for the share. 
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    file.delete_file_if_exists();

## <a name="how-to-delete-a-directory"></a>Comment : supprimer un répertoire

Suppression d’un répertoire est une tâche simple, bien qu’il convient de noter que vous ne pouvez pas supprimer un répertoire contenant des fichiers ou autres répertoires.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // Get a reference to the directory.
    azure::storage::cloud_file_directory directory = 
      share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Get a reference to the subdirectory you want to delete.
    azure::storage::cloud_file_directory sub_directory =
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    
    // Delete the subdirectory and the sample directory.
    sub_directory.delete_directory_if_exists();
    
    directory.delete_directory_if_exists();

## <a name="how-to-delete-a-share"></a>Comment : supprimer un partage

Suppression d’un partage résulte de l’appel de la méthode **delete_if_exists** un objet cloud_file_share. Voici des exemples de code qui a.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // delete the share if exists
    share.delete_share_if_exists();

## <a name="set-the-maximum-size-for-a-file-share"></a>Définir la taille maximale d’un partage de fichiers

Vous pouvez définir le quota (ou la taille maximale autorisée) pour un partage de fichiers, en gigaoctets. Vous pouvez également vérifier la quantité de données se trouve actuellement sur le partage.

En définissant le quota pour un partage, vous pouvez limiter la taille totale des fichiers stockés sur le partage. Si la taille totale des fichiers sur le partage dépasse le quota de définir sur le partage, puis clients ne pourront pas augmenter la taille des fichiers existants ou en créer de nouveaux fichiers, à moins que ces fichiers sont vides.

L’exemple ci-dessous montre comment vérifier l’utilisation actuelle pour un partage et comment définir le quota pour le partage.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    if (share.exists())
    {
        std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();
    
        //This line sets the quota to 2560GB
        share.resize(2560);
    
        std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();
    
    }

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Générer une signature d’un accès partagé d’un fichier ou le partage de fichiers

Vous pouvez générer une signature accès partagé (sa) pour un partage de fichiers ou un fichier. Vous pouvez également créer une stratégie d’accès partagé sur un partage de fichiers pour gérer les accès partagé signatures. Création d’une stratégie d’accès partagé est recommandé, car il fournit un moyen de révoquer les associations de sécurité s’il doit être compromis.

L’exemple suivant crée une stratégie d’accès partagé sur un partage, puis utilise cette stratégie permettent de fournir les contraintes pour un sa sur un fichier dans le partage.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client and get a reference to the share
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    if (share.exists())
    {
        // Create and assign a policy
        utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

        azure::storage::file_shared_access_policy sharedPolicy = 
          azure::storage::file_shared_access_policy();

        //set permissions to expire in 90 minutes
        sharedPolicy.set_expiry(utility::datetime::utc_now() + 
          utility::datetime::from_minutes(90));

        //give read and write permissions
        sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

        //set permissions for the share
        azure::storage::file_share_permissions permissions; 

        //retrieve the current list of shared access policies
        azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;
        
        //add the new shared policy
        policies.insert(std::make_pair(policy_name, sharedPolicy));

        //save the updated policy list
        permissions.set_policies(policies);
        share.upload_permissions(permissions);

        //Retrieve the root directory and file references
        azure::storage::cloud_file_directory root_dir = 
          share.get_root_directory_reference();
        azure::storage::cloud_file file = 
          root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

        // Generate a SAS for a file in the share 
        //  and associate this access policy with it.       
        utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);
        
        // Create a new CloudFile object from the SAS, and write some text to the file.     
        azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
        utility::string_t text = _XPLATSTR("My sample content");        
        file_with_sas.upload_text(text);        
        
        //Download and print URL with SAS.
        utility::string_t downloaded_text = file_with_sas.download_text();      
        ucout << downloaded_text << std::endl;      
        ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;
    
    }

Pour plus d’informations sur la création et l’utilisation de signatures accès partagé, reportez-vous [à l’aide de l’accès des Signatures (sa partagées)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le stockage Azure, Explorez ces ressources :

-   [Bibliothèque de Client de stockage pour C++](https://github.com/Azure/azure-storage-cpp)

-   [Explorateur de stockage Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)

-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
