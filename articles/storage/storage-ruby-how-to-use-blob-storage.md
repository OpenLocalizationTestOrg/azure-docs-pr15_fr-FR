<properties
    pageTitle="L’utilisation de stockage d’objets Blob (stockage d’objets) à partir de Ruby | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec stockage d’objets Blob Azure (stockage d’objets)."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>Comment utiliser le stockage Blob de Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage d’objets Blob Azure est un service qui stocke des données non structurées dans le cloud comme objets/des objets BLOB. Stockage d’objets BLOB peut stocker n’importe quel type de texte ou des données binaires, tel qu’un document, un fichier multimédia ou un programme d’installation d’application. Stockage d’objets BLOB est également appelée stockage d’objets.

Ce guide vous montrent comment effectuer des scénarios courants à l’aide de stockage d’objets Blob. Ces exemples sont écrits à l’aide de l’API Ruby. Les scénarios présentés incluent des objets BLOB de **téléchargement, de liste, téléchargement** et la **suppression** .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Créer une application prononciation

Créer une application prononciation. Pour plus d’informations, voir [Ruby sur application Web Rails sur un ordinateur virtuel Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Configurez votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous devez télécharger et utiliser le package azure prononciation, qui comprend un ensemble de bibliothèques de commodité communiquer avec les services reste de stockage.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems permet d’obtenir le package

1. Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Tapez « azure d’installation marque » dans la fenêtre de commande pour installer la marque et les dépendances.

### <a name="import-the-package"></a>Importer le package

À l’aide de votre éditeur de texte, ajoutez le code suivant en haut du fichier prononciation dans laquelle vous souhaitez utiliser le stockage :

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Le module azure lira les variables d’environnement **AZURE\_stockage\_compte** et **AZURE\_stockage\_ACCESS_KEY** pour plus d’informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définis, vous devez spécifier les informations de compte avant d’utiliser **Azure::Blob::BlobService** avec le code suivant :

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


Pour obtenir ces valeurs à partir d’un classique ou le compte de stockage Gestionnaire de ressources dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Naviguez vers le compte de stockage que vous souhaitez utiliser.
3. Dans la carte de paramètres sur la droite, cliquez sur **Les touches d’accès**.
4. Dans la carte de touches d’accès qui s’affiche, vous verrez la touche d’accès rapide 1 et 2 touche d’accès rapide. Vous pouvez utiliser des options suivantes. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers. 

Pour obtenir ces valeurs à partir d’un compte de stockage classique dans le portail Azure classique :

1. Connectez-vous au [portail Azure classique](https://manage.windowsazure.com).
2. Naviguez vers le compte de stockage que vous souhaitez utiliser.
3. Cliquez sur **Gérer les touches d’accès** dans la partie inférieure du volet de navigation.
4. Dans la boîte de dialogue qui apparaît, vous verrez le nom de compte de stockage, la clé primaire access et la touche d’accès secondaire. Pour touche d’accès rapide, vous pouvez utiliser l’option principal ou secondaire celui. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers.

## <a name="create-a-container"></a>Créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

L’objet **Azure::Blob::BlobService** vous permet d’utiliser des conteneurs et des objets BLOB. Pour créer un conteneur, utilisez la **créer\_container()** méthode.

L’exemple suivant crée un conteneur ou imprimer l’erreur cas échéant.

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Si vous souhaitez publier les fichiers dans le conteneur, vous pouvez définir des autorisations du conteneur.

Vous pouvez uniquement modifier la <strong>créer\_container()</strong> appel à passer le **: public\_access\_niveau** option :

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


Les valeurs valides pour la **: public\_access\_niveau** option sont :

* **blob :** Spécifie l’accès en lecture publique complète pour les données conteneur et blob. Les clients peuvent énumérer les objets BLOB dans le conteneur via demande anonyme, mais Impossible d’énumérer les conteneurs à l’intérieur du compte de stockage.

* **conteneur :** Spécifie l’accès en lecture public pour les objets BLOB. Données BLOB dans ce conteneur peuvent être lus par le biais demande anonyme, mais les données de conteneur ne seront pas disponibles. Clients Impossible d’énumérer les objets BLOB dans le conteneur via demande anonyme.

Par ailleurs, vous pouvez modifier le niveau d’accès public d’un conteneur à l’aide de **définir\_conteneur\_acl()** méthode pour spécifier le niveau d’accès public.

L’exemple suivant modifie le niveau d’accès public au **conteneur**:

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Pour télécharger le contenu dans un blob, utilisez la **créer\_bloc\_blob()** méthode pour créer le blob, utiliser un fichier ou une chaîne en tant que le contenu de l’objet blob.

Le code suivant télécharge le fichier **test.png** comme un nouveau blob appelé « image-blob » dans le conteneur.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>Répertorier les objets BLOB dans un conteneur

Pour répertorier les conteneurs, utilisez la méthode **list_containers()** .
Pour répertorier les objets BLOB dans un conteneur, utilisez **liste\_blobs()** méthode.

Ceci affiche les URL de tous les objets BLOB dans tous les conteneurs du compte.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger des objets BLOB, utilisez la **obtenir\_blob()** méthode permettant d’extraire le contenu.

L’exemple suivant illustre l’utilisation de **obtenir\_blob()** télécharger le contenu de « image blob » et d’écrire dans un fichier local.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>Supprimer un Blob
Enfin, pour supprimer un blob, utilisez la **Supprimer\_blob()** méthode. L’exemple suivant montre comment supprimer un blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les tâches de stockage plus complexes, suivez ces liens :

- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Référentiel de [Azure SDK pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) GitHub
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
