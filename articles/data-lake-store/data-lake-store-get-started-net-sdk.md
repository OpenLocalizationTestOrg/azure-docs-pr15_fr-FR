<properties
   pageTitle="Utiliser des données Lake Store .NET SDK pour développer des applications | Microsoft Azure"
   description="Utiliser Azure données Lake Store .NET SDK pour développer des applications"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Prise en main Azure données Lake Store à l’aide du Kit de développement .NET

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Azure infrastructure du langage commun](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser le [Kit de développement de .NET Azure données Lake Store](https://msdn.microsoft.com/library/mt581387.aspx) pour effectuer les opérations de base telles que la création de dossiers, chargement et téléchargement des fichiers de données, etc.. Pour plus d’informations sur les données Lake, voir [Azure données Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Conditions préalables

* **Visual Studio 2013 ou 2015**. Les instructions ci-dessous utilisent Visual Studio 2015.

* **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

* **Compte azure données Lake Store**. Pour obtenir des instructions sur la création d’un compte, voir [prise en main Azure données Lake Store](data-lake-store-get-started-portal.md)

* **Créer une Application Azure Active Directory**. Vous utilisez l’application Azure AD pour l’authentification de l’application de données Lake Store avec Azure AD. Il existe différentes approches pour vous authentifier avec Azure AD, qui sont **l’authentification de l’utilisateur final** ou **service à service**. Pour plus d’informations sur la façon de s’authentifier et des instructions, voir [authentifier avec données Lake Store à l’aide de Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Créer une application .NET

1. Ouvrez Visual Studio et créez une application console.

2. Dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.

3. À partir de **Projet**, tapez ou sélectionnez les valeurs suivantes :

  	| Propriété | Valeur                       |
  	|----------|-----------------------------|
  	| Catégorie | Modèles/Visual c# / Windows |
  	| Modèle | Application console         |
  	| Nom     | CreateADLApplication        |

4. Cliquez sur **OK** pour créer le projet.

5. Ajouter les packages Nuget à votre projet.

    1. Cliquez sur le nom du projet dans l’Explorateur de solutions, cliquez sur **Gérer les Packages NuGet**.
    2. Dans l’onglet **Gestionnaire de Package Nuget** , assurez-vous que la **source du Package** est défini sur **nuget.org** et cette case à cocher **inclure la version préliminaire** est sélectionnée.
    3. Rechercher et installer les packages NuGet suivants :

        * `Microsoft.Azure.Management.DataLake.Store`-Ce didacticiel utilise v0.12.5 preview.
        * `Microsoft.Azure.Management.DataLake.StoreUploader`-Ce didacticiel utilise v0.10.6 preview.
        * `Microsoft.Rest.ClientRuntime.Azure.Authentication`-Ce didacticiel utilise v2.2.8 preview.

        ![Ajouter une source de Nuget] (./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Créer un nouveau compte Lake de données Azure")

    4. Fermez le **Gestionnaire de Package Nuget**.

6. Ouvrez **Program.cs**, supprimez le code existant et puis incluez les instructions suivantes pour ajouter des références aux espaces de noms.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. Déclarer les variables comme indiqué ci-dessous, puis fournir les valeurs de données Lake enregistrer le nom et le nom du groupe de ressources qui existent déjà. En outre, vérifiez que le chemin d’accès local et le nom de fichier que vous fournissez ici doit exister sur l’ordinateur. Ajoutez l’extrait de code suivantes après les déclarations d’espace de noms.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";
                    
                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Dans les sections restantes de l’article, vous pouvez voir comment utiliser les méthodes .NET disponibles pour effectuer des opérations, telles que l’authentification, téléchargement de fichiers, etc..

## <a name="authentication"></a>Authentification

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Si vous utilisez l’authentification de l’utilisateur final (recommandée pour ce didacticiel)

Utilisez cette commande avec une Application « Native Client » AD Azure existante ; une est fournie ci-dessous pour vous. Pour vous aider à effectuer plus rapidement ce didacticiel, nous vous recommandons de qu'utiliser cette approche.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Deux choses à savoir sur cet extrait de code ci-dessus.

* Pour vous aider à effectuer le didacticiel plus rapidement, cet extrait utilise une une annonce Azure domaine et ID de client qui est disponible par défaut pour tous les abonnements Azure. Par conséquent, vous pouvez **utiliser cet extrait en tant que-se trouve dans votre application**.
* Toutefois, si vous ne souhaitez pas utiliser votre propre ID de client Azure AD domaine et d’application, vous devez créer une application native Azure AD puis utiliser le domaine AD Azure, ID de client et rediriger URI pour l’application que vous avez créé. Pour plus d’informations, voir [créer une Application Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) .

>[AZURE.NOTE] Les instructions dans les liens ci-dessus sont pour une application web Azure AD. Toutefois, les étapes sont exactement identiques même si vous avez choisi de créer une application cliente native à la place. 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Si vous utilisez l’authentification de service à service avec secret client 

L’extrait de code suivante peut servir à authentifier votre application en mode non interactif, en utilisant le client secrète / clé pour une identité d’application ou le service. Utilisez cette option avec une [Application de « Web App » Azure AD](../resource-group-create-service-principal-portal.md)existante.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Si vous utilisez l’authentification de service à service avec certificat

Une troisième option, l’extrait suivant peut servir à authentifier votre application en mode non interactif, en utilisant le certificat pour une identité d’application ou le service. Utilisez cette option avec une [Application de « Web App » Azure AD](../resource-group-create-service-principal-portal.md)existante.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Créer des objets client

L’extrait de code suivant crée le client de compte et le système de fichiers de données Lake Store objets, qui sont utilisés pour émettre des demandes du service.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Répertorier tous les comptes de données Lake Store au sein d’un abonnement

L’extrait suivant répertorie tous les comptes de données Lake Store au sein d’un abonnement Azure donné.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## <a name="create-a-directory"></a>Créer un répertoire

L’extrait de code suivant un `CreateDirectory` méthode que vous pouvez utiliser pour créer un répertoire au sein d’un compte de données Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Télécharger un fichier

L’extrait de code suivant un `UploadFile` méthode qui vous permet de télécharger des fichiers à un compte de données Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader`prend en charge récursive de téléchargement entre un chemin d’accès local et un chemin d’accès du fichier de données Lake Store.    

## <a name="get-file-or-directory-info"></a>Obtenir des informations de fichier ou un répertoire

L’extrait de code suivant un `GetItemInfo` méthode que vous pouvez utiliser pour récupérer des informations sur un fichier ou un répertoire disponible données Lake Store. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Liste des fichiers et les répertoires

L’extrait de code suivant un `ListItem` méthode qui permet de répertorier les fichiers et des répertoires dans un compte de données Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concaténation de fichiers

L’extrait de code suivant un `ConcatenateFiles` méthode que vous utilisez pour concaténer des fichiers. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Ajouter à un fichier

L’extrait de code suivant un `AppendToFile` méthode qui vous permet d’ajouter des données à un fichier déjà stocké dans un compte de données Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Télécharger un fichier

L’extrait de code suivant un `DownloadFile` méthode qui vous permet de télécharger un fichier à partir d’un compte de données Lake Store.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Référence du Kit de développement logiciel magasin Lake de données .NET](https://msdn.microsoft.com/library/mt581387.aspx)
- [Référence du reste données Lake magasin](https://msdn.microsoft.com/library/mt693424.aspx)
