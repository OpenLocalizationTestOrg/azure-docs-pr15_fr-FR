<properties
    pageTitle="Application avec du stockage blob (Java) local | Microsoft Azure"
    description="Apprenez à créer une application console qui télécharge une image à Azure, puis affiche l’image dans votre navigateur. Exemples de code en Java."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="on-premises-application-with-blob-storage"></a>Application en local avec stockage d’objets blob

## <a name="overview"></a>Vue d’ensemble

L’exemple suivant vous montre comment vous pouvez utiliser le stockage Azure pour stocker les images dans Azure. Le code dans cet article concerne une application console qui télécharge une image à Azure, puis crée un fichier HTML qui affiche l’image dans votre navigateur.

## <a name="prerequisites"></a>Conditions préalables

- Un développeur Kit JDK (Java), version 1,6 ou version ultérieure est installé.
- Le Kit de développement Azure est installé.
- Le fichier JAR pour les bibliothèques Azure pour Java et n’importe quel bocaux dépendance applicable, est installés et se trouvent dans le chemin d’accès de build utilisé par votre compilateur Java. Pour plus d’informations sur l’installation les bibliothèques Azure pour Java, consultez [le Kit de développement Azure pour Java de téléchargement](java-download-azure-sdk.md).
- Un compte de stockage Azure a été défini. Le nom du compte et la clé de compte pour le compte de stockage servira par le code dans cet article. Découvrez [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) pour plus d’informations sur la création d’un compte de stockage et [Afficher et copier stockage touches d’accès rapide](storage-create-storage-account.md#view-and-copy-storage-access-keys) pour plus d’informations sur la récupération de la clé de compte.

- Vous avez créé un fichier image locale nommé stockées sur le lecteur c: chemin d’accès\\myimages\\image1.jpg. Par ailleurs, modifiez le constructeur   **FileInputStream** dans l’exemple pour utiliser un nom de fichier et le chemin image différente.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Pour utiliser le stockage blob Azure pour télécharger un fichier

Une procédure pas à pas est présentée ici. Si vous voulez passer directement, l’ensemble du code est présenté plus loin dans cet article.

Commencez le code en y compris les importations pour les cours de stockage Azure de base, les classes clientes blob Azure, les classes IO Java et la classe **URISyntaxException** .

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Déclarez une classe nommée **StorageSample**et inclure le crochet ouvrant, **{**.

    public class StorageSample {

Dans la classe **StorageSample** , déclarer une variable chaîne qui contient le protocole de point de terminaison par défaut, le nom de votre compte de stockage et votre stockage touche d’accès rapide, comme indiqué dans votre compte de stockage Azure. Remplacer les valeurs d’espace réservé **votre\_compte\_nom** et **votre\_compte\_clé** par votre propre clé de compte nom et compte, respectivement.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Ajouter dans votre déclaration pour **principale**, inclure un bloc **try** et inclure les parenthèses ouvertes nécessaires, **{**.

    public static void main(String[] args)
    {
        try
        {

Déclarer des variables de type suivant (les descriptions sont pour comment ils sont utilisés dans cet exemple) :

-   **CloudStorageAccount**: utilisées pour initialisation de l’objet compte avec votre nom de compte de stockage Azure et la clé et créer l’objet de client blob.
-   **CloudBlobClient**: utilisé pour accéder au service blob.
-   **CloudBlobContainer**: permet de créer un conteneur blob, les objets BLOB dans le conteneur de la liste et supprimer le conteneur.
-   **CloudBlockBlob**: permet de télécharger un fichier image locale au conteneur.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Assigner une valeur à la variable de **compte** .

    account = CloudStorageAccount.parse(storageConnectionString);

Assigner une valeur à la variable **serviceClient** .

    serviceClient = account.createCloudBlobClient();

Assigner une valeur à la variable de **conteneur** . Nous allons obtenir une référence à un conteneur nommé **mise en route**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Créez le conteneur. Cette méthode crée le conteneur s’il n’existe (et renvoie la **valeur true**). Si le conteneur existe, elle retourne **false**. Une suggestion pour **createIfNotExists** est la méthode **créer** (qui renverra une erreur si le conteneur existe déjà).

    container.createIfNotExists();

Définir l’accès anonyme pour le conteneur.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenir une référence à la blob bloc, qui représente le blob dans le stockage Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Utilisez le constructeur de **fichier** pour obtenir une référence au fichier créé en local que vous allez télécharger. Vérifiez que vous avez créé ce fichier avant d’exécuter le code.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Téléchargez le fichier local via un appel à la méthode **CloudBlockBlob.upload** . Le premier paramètre à la méthode **CloudBlockBlob.upload** est un objet **FileInputStream** qui représente le fichier local est téléchargé sur le stockage Azure. Le deuxième paramètre est la taille, en octets, du fichier.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Appeler une fonction d’assistance nommée **MakeHTMLPage**, pour transformer une page HTML simple qui contient un ** &lt;image&gt; ** élément avec la source de la valeur du blob figure à présent dans votre compte de stockage Azure. Le code de **MakeHTMLPage** nous aborderons plus loin dans cet article.

    MakeHTMLPage(container);

Imprimer un message d’état et des informations sur la page HTML créée.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Fermez le bloc **try** en insérant un crochet fermant : **}**

Gérer les exceptions suivantes :

-   **FileNotFoundException**: peut être levée par les constructeurs **FileInputStream** ou **FileOutputStream** .
-   **StorageException**: peut être renvoyée par la bibliothèque de stockage client Azure.
-   **URISyntaxException**: peut être renvoyée par la méthode **ListBlobItem.getUri** .
-   **Exception**: gestion des exceptions générique.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Fermez **principale** en insérant un crochet fermant : **}**

Créez une méthode nommée **MakeHTMLPage** pour créer une page HTML de base. Cette méthode implique un paramètre de type **CloudBlobContainer**, qui permet de parcourir la liste des objets BLOB téléchargés. Cette méthode lève les exceptions de type **FileNotFoundException**, qui peut être levée par le constructeur **FileOutputStream** et **URISyntaxException**, qui peut être renvoyée par la méthode **ListBlobItem.getUri** . Inclure la parenthèse ouvrante, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Créer un fichier local nommé **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Écrire dans le fichier local en ajoutant dans le ** &lt;html&gt;**, ** &lt;en-tête&gt;**, et ** &lt;corps&gt; ** éléments.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Parcourir la liste des objets BLOB téléchargés. Pour chaque blob, dans la page HTML, créez un ** &lt;img&gt; ** élément dont l’attribut **src** envoyée à l’URI du blob telle qu’elle existe dans votre compte de stockage Azure.
Bien que vous avez ajouté une seule image dans cet exemple, si vous avez ajouté plus, ce code serait parcourir celles-ci.

Pour simplifier, cela suppose que chaque blob téléchargé est une image. Si vous avez mis à jour des objets BLOB autres que des images ou des objets BLOB de page à la place des objets BLOB bloc, réglez le code.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Fermer la ** &lt;corps&gt; ** élément et le ** &lt;html&gt; ** élément.

    stream.println("</body>");
    stream.println("</html>");

Fermez le fichier local.

    stream.close();

Fermer **MakeHTMLPage** en insérant un crochet fermant : **}**

Fermer **StorageSample** en insérant un crochet fermant : **}**

Voici le code complet de cet exemple. N’oubliez pas de modifier les valeurs d’espace réservé **votre\_compte\_nom** et **votre\_compte\_clé** à utiliser votre nom de compte et votre clé de compte, respectivement.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

En plus de télécharger votre fichier d’image local vers le stockage Azure, l’exemple de code crée un fichier local namedindex.html, que vous pouvez ouvrir dans votre navigateur pour afficher votre image téléchargée.

Étant donné que le code contient votre nom de compte et votre clé de compte, vérifiez que votre code source est sécurisé.

## <a name="to-delete-a-container"></a>Pour supprimer un conteneur

Étant donné que vous êtes chargé pour le stockage, vous souhaiterez peut-être une fois que vous avez terminé de supprimer le conteneur de **mise en route** expérimenter cet exemple. Pour supprimer un conteneur, utilisez la méthode **CloudBlobContainer.delete** .

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Pour appeler la méthode **CloudBlobContainer.delete** , le processus d’initialisation objets **CloudStorageAccount**, **ClodBlobClient**et **CloudBlobContainer** est identique à celle indiquée pour la méthode **createIfNotExist** . Voici un exemple complet qui supprime le conteneur nommé **mise en route**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Pour une vue d’ensemble des autres classes de stockage blob et des méthodes, voir [comment utiliser le stockage Blob de Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- [Stockage Azure SDK pour Java](https://github.com/azure/azure-storage-java)
- [Référence du Kit de développement logiciel Client stockage Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
