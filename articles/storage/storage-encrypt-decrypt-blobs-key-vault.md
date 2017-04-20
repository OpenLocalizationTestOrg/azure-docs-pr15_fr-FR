<properties
    pageTitle="Didacticiel : Chiffrer et déchiffrer des objets BLOB Microsoft Azure Storage à l’aide de l’archivage sécurisé de clé Azure | Microsoft Azure"
    description="Ce didacticiel vous explique comment chiffrer et déchiffrer un blob en utilisant le chiffrement côté client pour le stockage Microsoft Azure avec l’archivage sécurisé de clé Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Didacticiel : Chiffrer et déchiffrer des objets BLOB Microsoft Azure Storage à l’aide de l’archivage sécurisé de clé Azure

## <a name="introduction"></a>Introduction

Ce didacticiel décrit comment rendre utilisation du chiffrement de stockage côté client avec l’archivage sécurisé de clé Azure. Il vous explique comment chiffrer et déchiffrer un blob dans une application console à l’aide de ces technologies.

**Durée estimée d’exécution :** 20 minutes

Pour plus d’informations générales sur l’archivage sécurisé de clé Azure, consultez [Quel est l’archivage sécurisé de clé Azure ?](../key-vault/key-vault-whatis.md).

Pour des informations générales sur le chiffrement côté client pour le stockage Azure, voir [le chiffrement côté Client et l’archivage sécurisé d’Azure clé pour le stockage Microsoft Azure](storage-client-side-encryption.md).


## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez disposer des éléments suivants :

- Un compte de stockage Azure
- Visual Studio 2013 ou version ultérieure
- PowerShell Azure


## <a name="overview-of-client-side-encryption"></a>Vue d’ensemble de chiffrement côté client

Pour une vue d’ensemble du chiffrement côté client pour le stockage Azure, voir [le chiffrement côté Client et l’archivage sécurisé d’Azure clé pour le stockage Microsoft Azure](storage-client-side-encryption.md)

Voici une brève description du fonctionne de chiffrement côté client :

1. Le client de stockage Azure SDK génère une clé de chiffrement de contenu (CEK), ce qui correspond à une clé symétrique usage unique.
2. Données client sont chiffrées à l’aide de cette CEK.
3. Le CEK est ensuite encapsulé (chiffrées) à l’aide de la clé de chiffrement clés (KEK). La KEK est identifié par un identificateur de clé et peut être une paire de clés asymétriques ou une clé symétrique et peut être gérée localement ou stockée dans l’archivage sécurisé de clé Azure. Le client de stockage lui-même n’a jamais accès à la KEK. Il appelle simplement l’algorithme de clé habillage qui est fourni par l’archivage sécurisé clé. Clients peuvent choisir d’utiliser des fournisseurs personnalisés pour clé habillage/dévoilement s’ils le souhaitent.
4. Les données chiffrées sont ensuite téléchargées au service de stockage Azure.


## <a name="set-up-your-azure-key-vault"></a>Configurer votre l’archivage sécurisé de clé Azure
Afin de procéder à ce didacticiel, vous devez effectuer les étapes suivantes, qui sont décrites dans le didacticiel [prise en main l’archivage sécurisé de clé Azure](../key-vault/key-vault-get-started.md):

- Créer un archivage sécurisé clé.
- Ajouter une clé ou un code secret à l’archivage sécurisé clé.
- Enregistrer une application avec Azure Active Directory.
- Autorisez l’application à utiliser la clé ou le code secret.

Prenez note de l’identifiant du client et ClientSecret qui a été généré lors de l’enregistrement d’une application avec Azure Active Directory.

Créer les deux clés dans l’archivage sécurisé clé. Pour le reste de ce didacticiel, nous supposons que vous avez utilisé les noms suivants : ContosoKeyVault et TestRSAKey1.


## <a name="create-a-console-application-with-packages-and-appsettings"></a>Créer une application console avec packages et AppSettings

Dans Visual Studio, créez une nouvelle application console.

Ajoutez des packages nuget nécessaires dans la Console Gestionnaire de Package.

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


Ajouter AppSettings à App.Config.

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

Ajoutez le code suivant `using` instructions et veillez à ajouter une référence à System.Configuration au projet.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Ajouter une méthode pour obtenir un jeton à votre application console

La méthode suivante est utilisée par les classes de l’archivage sécurisé clé qui doivent s’authentifier pour accéder à votre l’archivage sécurisé clé.

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>Accéder à stockage et l’archivage sécurisé clé dans votre programme

Dans la fonction Main, ajoutez le code suivant.

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modèles d’objet l’archivage sécurisé clés
>
>Il est important de comprendre qu’il existe réellement deux l’archivage sécurisé clé modèles d’objets importants : un est basé sur l’API REST (espace de noms KeyVault) et l’autre est une extension pour le chiffrement côté client.

> Le Client de l’archivage sécurisé clé interagit avec l’API REST et comprend les touches Web JSON et codes secrets pour les deux types d’éléments contenus dans l’archivage sécurisé clé.

> Les Extensions de l’archivage sécurisé clé sont classes qui semblent spécifiquement créées pour le chiffrement côté client dans le stockage Azure. Ils contiennent une interface pour les clés (IKey) et classes basées sur le concept d’un programme de résolution de clé. Il existe deux mises en œuvre de IKey dont vous avez besoin de savoir : RSAKey et SymmetricKey. Maintenant fur correspondent avec les événements qui sont contenues dans un archivage sécurisé clé, mais à ce stade qu’ils sont indépendants classes (de sorte que le code Secret récupérés par le Client de l’archivage sécurisé clé et clé n’implémentent pas IKey).


## <a name="encrypt-blob-and-upload"></a>Chiffrer blob et télécharger
Ajoutez le code suivant pour chiffrer un blob et téléchargez-le sur votre compte de stockage Azure. La méthode **ResolveKeyAsync** qui est utilisée renvoie un IKey.


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


Voici une capture d’écran à partir du [Portail classique Azure](https://manage.windowsazure.com) pour un blob qui a été chiffré à l’aide de chiffrement côté client avec une clé stockée dans la clé de l’archivage sécurisé. La propriété **ID de la clé** est l’URI de la clé dans l’archivage sécurisé clé qui se comporte comme le KEK. La propriété **EncryptedKey** contient la version cryptée de la CEK.

![Capture d’écran montrant les métadonnées Blob qui inclut des métadonnées de chiffrement][1]

> [AZURE.NOTE] Si vous examinez le constructeur BlobEncryptionPolicy, vous verrez qu’il peut accepter une clé et/ou un programme de résolution. N’oubliez pas que pour le moment, vous ne pouvez pas utiliser un utilitaire de résolution pour le chiffrement, car elle n’a pas actuellement en charge une clé par défaut.



## <a name="decrypt-blob-and-download"></a>Déchiffrer blob de téléchargement
Le déchiffrement est vraiment lorsque l’utilisation des classes vider représentatives. L’ID de la clé de chiffrement étant associé au blob dans ses métadonnées, il est inutile de récupérer la clé et n’oubliez pas de l’association entre la touche et blob. Il vous suffit de vous assurer que la clé restera dans l’archivage sécurisé clé.   

La clé privée d’une clé RSA est conservé dans l’archivage sécurisé clé, et pour le déchiffrement se produise, la touche chiffrés à partir des métadonnées blob qui contient le CEK sont envoyée à l’archivage sécurisé clé pour le déchiffrement.

Ajoutez le code suivant pour déchiffrer le blob que vous venez de télécharger.

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Il existe deux autres types de programmes de résolution pour faciliter la gestion de clés, y compris : AggregateKeyResolver et CachingKeyResolver.


## <a name="use-key-vault-secrets"></a>Utiliser secrets de l’archivage sécurisé clé
La façon d’utiliser un code secret avec chiffrement côté client est via la classe SymmetricKey un secret étant pour l’essentiel une clé symétrique. Cependant, comme indiqué ci-dessus, un code secret dans l’archivage sécurisé clé ne correspond pas exactement à une SymmetricKey. Il existe quelques points à comprendre :


- La clé dans une SymmetricKey doit être une longueur fixe : 128, 192, 256, 384 ou 512 bits.
- La clé dans une SymmetricKey doit être en base 64 codé.
- Un code secret l’archivage sécurisé clé qui sera utilisé comme un SymmetricKey doit avoir un Type de contenu de « application/octet-stream » dans l’archivage sécurisé clé.

Voici un exemple de PowerShell de création d’un code secret dans l’archivage sécurisé clé qui peut être utilisé comme un SymmetricKey.
Remarque : La valeur codée en dur, $key, est uniquement à des fins de démonstration. Dans votre propre code vous souhaiterez générer cette touche.

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Dans votre application console, vous pouvez utiliser le même appel en tant qu’avant pour récupérer ce code secret comme un SymmetricKey.

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Voilà. Profitez !

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Microsoft Azure stockage avec c#, voir [Microsoft Azure stockage Client bibliothèque pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Pour plus d’informations sur l’API REST Blob, voir [L’API REST Blob Service](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Pour obtenir les dernières informations sur Microsoft Azure stockage, accédez au [Blog de l’équipe Microsoft Azure stockage](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
