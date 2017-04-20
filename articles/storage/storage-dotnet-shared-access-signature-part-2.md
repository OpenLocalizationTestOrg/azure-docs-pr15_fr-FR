<properties
    pageTitle="Créer et utiliser une associations de sécurité avec le stockage Blob | Microsoft Azure"
    description="Ce didacticiel vous montre comment créer des signatures accès partagé pour une utilisation avec stockage Blob et comment les utiliser à partir de vos applications clientes."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Partagé Signatures Access, partie 2 : Créer et utiliser une associations de sécurité avec le stockage Blob

## <a name="overview"></a>Vue d’ensemble

[Partie 1](storage-dotnet-shared-access-signature-part-1.md) de ce didacticiel exploré partagé signatures access (sa) et expliqués meilleures pratiques pour les utiliser. Partie 2 vous montre comment générer, puis utilisez les signatures accès partagé avec stockage d’objets Blob. Les exemples sont écrits en c# et utilisent la bibliothèque de Client de stockage Azure pour .NET. Les scénarios présentés incluent ces aspects de l’utilisation de signatures accès partagé :

- Générer une signature d’un accès partagé sur un conteneur
- Générer une signature d’un accès partagé sur un blob
- Création d’une stratégie d’accès stockée pour gérer les signatures sur les ressources d’un conteneur
- Tester les signatures accès partagé à partir d’une application cliente

## <a name="about-this-tutorial"></a>À propos de ce didacticiel

Dans ce didacticiel, nous allons nous concentrer sur la création d’un accès partagé signatures pour conteneurs et des objets BLOB en créant des deux applications console. La première application console génère signatures accès partagé sur un conteneur et sur un blob. Cette application a connaissance des clés de compte de stockage. La deuxième application console, qui va agir en tant qu’une application client, accède au conteneur et ressources blob à l’aide de signatures accès partagé créés avec la première demande. Cette application utilise les signatures accès partagé uniquement pour authentifier son accès au conteneur et blob ressources : il n’a pas connaissance des clés de compte.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Partie 1 : Créer une Application Console pour générer des Signatures accès partagé

Tout d’abord, assurez-vous que vous disposez de la bibliothèque de Client de stockage Azure pour .NET est installé. Vous pouvez installer le [package NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "package NuGet") contenant les assemblys plus à jour de la bibliothèque de client ; Il s’agit de la méthode recommandée pour s’assurer que vous disposez les solutions plus récentes. Vous pouvez également télécharger la bibliothèque de client dans le cadre de la version la plus récente du [Kit de développement logiciel Azure pour .NET](https://azure.microsoft.com/downloads/).

Dans Visual Studio, créez une nouvelle application de console Windows et nommez-la **GenerateSharedAccessSignatures**. Ajouter des références à **Microsoft.WindowsAzure.Configuration.dll** et **Microsoft.WindowsAzure.Storage.dll**, à l’aide d’une des approches suivantes :

-   Si vous voulez installer le package NuGet, tout d’abord installer le [NuGet Client](https://docs.nuget.org/consume/installing-nuget). Dans Visual Studio, sélectionnez **projet | Gérer les Packages NuGet**, effectuer une recherche en ligne pour le **Stockage Azure**, puis suivez les instructions d’installation.
-   Vous pouvez également rechercher les assemblys dans votre installation du Kit de développement Azure et ajoutez des références à ces.

En haut du fichier Program.cs, ajoutez les instructions suivantes **à l’aide de** :

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Modifiez le fichier app.config pour qu’il contienne un paramètre de configuration avec une chaîne de connexion qui pointe vers votre compte de stockage. Votre fichier app.config doit ressembler à celui-ci :

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Générer une Signature d’un accès partagé URI pour un conteneur

Dans un premier temps, nous allons ajouter une méthode pour générer une signature accès partagé sur un nouveau conteneur. Dans ce cas la signature n’est pas associée à une stratégie d’accès stockées, afin qu’il porte sur l’URI les informations indiquant sa date d’expiration et les autorisations qui autorise le.

Tout d’abord, ajoutez le code à la méthode **Main()** pour authentifier l’accès à votre compte de stockage et créer un nouveau conteneur :

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

Ensuite, ajoutez une nouvelle méthode qui génère la signature d’un accès partagé pour le conteneur et retourne la signature URI :

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Ajoutez les lignes suivantes dans la partie inférieure de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, pour appeler **GetContainerSasUri()** et écrire la signature URI dans la fenêtre de la console :

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compiler et exécuter la signature d’un accès partagé URI pour le nouveau conteneur de sortie. L’URI sera semblable à l’URI suivant :       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Une fois que vous avez exécuté le code, la signature d’accès partagé que vous avez créé dans le conteneur sera valide pour les prochaines 24 heures. La signature autorise un client à des objets BLOB de liste dans le conteneur et d’écrire un nouveau blob dans le conteneur.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Générer une Signature d’un accès partagé URI pour un Blob

Ensuite, nous allons écrire du code similaire pour créer un nouveau blob dans le conteneur et générer une signature d’un accès partagé pour qu’elle. Cette signature accès partagé n’est pas associée à une stratégie d’accès stockées, afin qu’il comporte l’heure de début, délai d’expiration et les informations d’autorisation de l’URI.

Ajouter une nouvelle méthode qui crée un nouveau blob et écrire du texte, puis génère une signature accès partagé et renvoie la signature URI :

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

En bas de la méthode **Main()** , ajoutez les lignes suivantes pour appeler **GetBlobSasUri()**, avant l’appel à **console.ReadLine ()**et écrire la signature d’un accès partagé URI dans la fenêtre de la console :    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compiler et exécuter la signature d’un accès partagé URI pour le nouveau blob de sortie. L’URI sera semblable à l’URI suivant :

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Créer une stratégie d’accès stockée sur le conteneur

Maintenant nous allons créer une stratégie d’accès stockée sur le conteneur, qui définit les contraintes de toutes les signatures accès partagé qui lui sont associées.

Dans les exemples précédents, nous avons spécifié l’heure de début (implicitement ou explicitement), le délai d’expiration et les autorisations sur la signature d’un accès partagé URI lui-même. Dans les exemples suivants, nous spécifierez ces sur la stratégie d’accès stockées et non sur la signature d’un accès partagé. Cela permet de modifier ces contraintes sans relançant la signature d’un accès partagé.

Il est possible d’avoir une ou plusieurs des contraintes de la signature d’un accès partagé et le reste sur la stratégie d’accès stockée. Toutefois, vous ne pouvez spécifier l’heure de début, date d’expiration et autorisations dans un seul endroit ou l’autre ; par exemple, vous ne pouvez pas spécifier des autorisations sur la signature d’un accès partagé et également les spécifier dans la stratégie d’accès stockée.

Notez que lorsque vous ajoutez une stratégie d’accès à un conteneur, vous devez obtenir les autorisations existantes du conteneur, ajouter la nouvelle stratégie d’accès et définissez les autorisations du conteneur.

Ajouter une nouvelle méthode qui crée une nouvelle stratégie d’accès stockée sur un conteneur et retourne le nom de la stratégie :

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

En bas de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, ajoutez les lignes suivantes pour désactiver au préalable les stratégies d’accès existantes et puis appelez la méthode **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Notez que lorsque vous désactivez les stratégies d’accès dans un conteneur, vous devez tout d’abord obtenir les autorisations existantes du conteneur, puis désactivez les autorisations, puis définissez les autorisations à nouveau.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Générer une Signature d’un accès partagé URI sur le conteneur qui utilise une stratégie d’accès

Ensuite, nous allons créer une autre signature accès partagé sur le conteneur que nous avons créé précédemment, mais cette fois, que nous allons associer la signature à la stratégie d’accès que nous avons créé dans l’exemple précédent.

Ajouter une nouvelle méthode pour générer une autre signature accès partagé sur le conteneur :

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

En bas de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, ajoutez les lignes suivantes pour appeler la méthode **GetContainerSasUriWithPolicy** :

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Générer une Signature d’un accès partagé URI sur le Blob qui utilise une stratégie d’accès

Pour finir, nous allons ajouter une méthode similaire pour créer un autre blob et générer une signature d’un accès partagé associée à une stratégie d’accès.

Ajouter une nouvelle méthode pour créer un blob et générer une signature accès partagé :

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

En bas de la méthode **Main()** , avant l’appel à **console.ReadLine ()**, ajoutez les lignes suivantes pour appeler la méthode **GetBlobSasUriWithPolicy** :    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

La méthode **Main()** doit maintenant ressembler à ceci dans son intégralité. Exécutez-le pour écrire la signature d’un accès partagé MU dans la fenêtre de la console, puis copiez et collez-les dans un fichier texte à utiliser dans la deuxième partie de ce didacticiel.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Lorsque vous exécutez l’application console GenerateSharedAccessSignatures, vous verrez sortie similaire à ce qui suit dans la fenêtre de la console. Voici les signatures accès partagé que vous allez utiliser dans la partie 2 du didacticiel.

![associations de sécurité-console-sortie-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Partie 2 : Créer une Application Console pour tester les Signatures accès partagé

Pour tester les signatures accès partagé créés dans les exemples précédents, nous allons créer une deuxième application console qui utilise les signatures pour effectuer les opérations sur le conteneur et sur un blob.

> [AZURE.NOTE] Si plus de 24 heures se sont écoulés depuis la fin de la première partie de ce didacticiel, les signatures que vous généré ne sera plus valides. Dans ce cas, vous devez exécuter le code dans la première application console pour générer des signatures novateur accès partagé à utiliser dans la deuxième partie de ce didacticiel.

Dans Visual Studio, créez une nouvelle application de console Windows et nommez-la **ConsumeSharedAccessSignatures**. Ajouter des références à **Microsoft.WindowsAzure.Configuration.dll** et **Microsoft.WindowsAzure.Storage.dll**, comme vous le faisiez précédemment.

En haut du fichier Program.cs, ajoutez les instructions suivantes **à l’aide de** :

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Dans le corps de la méthode **Main()** , ajoutez les constantes suivantes et mettre à jour les valeurs correspondantes aux signatures accès partagé que vous avez généré dans la partie 1 du didacticiel.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Ajouter une méthode pour essayer les opérations de conteneur à l’aide d’une Signature accès partagé

Ensuite, nous allons ajouter une méthode qui teste certaines opérations représentatives conteneur à l’aide d’une signature accès partagé sur le conteneur. Notez que la signature d’un accès partagé est utilisée pour renvoyer une référence au conteneur, l’authentification de l’accès au conteneur en fonction de la signature uniquement.

Ajoutez la méthode suivante à Program.cs :

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Mettre à jour la méthode **Main()** pour appeler **UseContainerSAS()** avec deux signatures accès partagé que vous avez créé dans le conteneur :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Ajouter une méthode pour essayer les opérations de Blob à l’aide d’une Signature accès partagé

Pour finir, nous allons ajouter une méthode qui teste certaines opérations représentatives blob à l’aide d’une signature accès partagé sur le blob. Dans ce cas, nous utilisons le constructeur **CloudBlockBlob(String)**, en passant dans la signature d’un accès partagé, pour renvoyer une référence à l’objet blob. Aucune autre authentification n’est requise ; Il est basé sur la signature uniquement.

Ajoutez la méthode suivante à Program.cs :

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Mettre à jour la méthode **Main()** pour appeler **UseBlobSAS()** avec deux signatures accès partagé que vous avez créé dans le blob :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Exécutez l’application console et observez la sortie pour afficher les opérations qui sont autorisées pour les signatures. Le résultat dans la fenêtre de la console est semblable à ce qui suit :

![associations de sécurité-console-sortie-2][sas-console-output-2]

## <a name="next-steps"></a>Étapes suivantes

[Accès partagé Signatures, partie 1 : Présentation du modèle associations de sécurité](storage-dotnet-shared-access-signature-part-1.md)

[Gérer l’accès anonyme en lecture à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md)

[Délégation d’accès avec une Signature d’un accès partagé (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Présentation tableau et file d’attente associations de sécurité](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
