<properties
    pageTitle="À l’aide de PowerShell Azure avec stockage Azure | Microsoft Azure"
    description="Découvrez comment utiliser les applets de commande PowerShell Azure pour le stockage Azure pour créer et gérer les comptes de stockage ; travailler avec des objets BLOB, des tableaux, des files d’attente et des fichiers ; configurer et analytique de stockage de la requête et créer des signatures accès partagé."
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>À l’aide de PowerShell Azure avec stockage Azure

## <a name="overview"></a>Vue d’ensemble

PowerShell Azure est un module qui fournit des applets de commande pour gérer Azure à utiliser Windows PowerShell. Il s’agit d’un environnement de ligne de commande basée sur les tâches et le langage de script conçues spécialement pour l’administration du système. Avec PowerShell, vous pouvez facilement contrôler et d’automatiser l’administration de vos applications et services Azure. Par exemple, vous pouvez utiliser les applets de commande pour effectuer les mêmes tâches que vous pouvez effectuer à partir du [Portail Azure](https://portal.azure.com).

Dans ce guide, nous allons découvrir comment utiliser les [Applets de commande de stockage Azure](https://msdn.microsoft.com/library/azure/mt269418.aspx) pour effectuer diverses tâches de développement et d’administration avec le stockage Azure.

Ce guide suppose que vous avez expérience antérieure à l’aide de [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/) et [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Le guide fournit un certain nombre de scripts pour illustrer l’utilisation de PowerShell avec stockage Azure. Vous devez mettre à jour les variables de script en fonction de votre configuration avant l’exécution de chaque script.

La première section de ce guide fournit un coup de œil au stockage Azure et PowerShell. Pour plus d’informations et des instructions, démarrer à partir de la [Configuration requise pour l’utilisation de PowerShell Azure avec stockage Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Prise en main de stockage Azure et PowerShell dans 5 minutes

Cette section vous montre comment accéder au stockage Azure via PowerShell dans 5 minutes.

**Aux Azure :** Obtenir un abonnement Microsoft Azure et un compte Microsoft associé à cet abonnement. Pour plus d’informations sur les options d’achat Azure, voir [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/), [Options d’achat](https://azure.microsoft.com/pricing/purchase-options/)et [Propose des membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network, BizSpark et des autres programmes Microsoft).

Pour plus d’informations sur les abonnements Azure, voir [affectation de rôles administrateur dans Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Après avoir créé un abonnement Microsoft Azure et un compte :**

1.  Téléchargez et installez [PowerShell Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.  Démarrer Windows PowerShell Scripting environnement intégré (ISE) : Votre ordinateur local, accédez au menu **Démarrer** . Tapez **Les outils d’administration** , cliquez sur Exécuter. Dans la fenêtre **Outils d’administration** , avec le bouton droit de **Windows PowerShell ISE**, cliquez sur **Exécuter en tant qu’administrateur**.
3.  Dans **Windows PowerShell ISE**, cliquez sur **fichier** > **Nouveau** pour créer un nouveau fichier de script.
4.  À présent, nous allons vous offre un script simple qui illustre les commandes PowerShell base pour accéder au stockage Azure. Le script demandera tout d’abord votre Azure informations d’identification de compte à ajouter votre Azure du compte à l’environnement local PowerShell. Ensuite, le script sera définir la valeur par défaut abonnement Azure et créez un nouveau compte de stockage dans Azure. Ensuite, le script crée un nouveau conteneur dans ce nouveau compte de stockage et télécharger un fichier image existant (blob) sur ce conteneur. Une fois le script répertorie tous les objets BLOB dans ce conteneur, il crée un nouveau dossier de destination dans votre ordinateur local et télécharger le fichier image.
5.  Dans la section de code suivantes, sélectionnez le script entre les remarques **#begin** et **#end**. Appuyez sur CTRL + C pour copier dans le Presse-papiers.

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  Dans **Windows PowerShell ISE**, appuyez sur CTRL + V pour copier le script. Cliquez sur **fichier** > **Enregistrer**. Dans la fenêtre de dialogue **Enregistrer sous** , tapez le nom du fichier de script, tels que « mystoragescript ». Cliquez sur **Enregistrer**.

6.  À présent, vous devez mettre à jour les variables de script en fonction de vos paramètres de configuration. Vous devez mettre à jour la variable **$SubscriptionName** avec vos propres abonnement. Vous pouvez conserver les autres variables comme indiqué dans le script ou les mettre à jour que vous le souhaitez.

    - **$SubscriptionName :** Vous devez mettre à jour cette variable avec votre propre nom de l’abonnement. Effectuez l’une des manières suivantes pour rechercher le nom de votre abonnement :

        un. Dans **Windows PowerShell ISE**, cliquez sur **fichier** > **Nouveau** pour créer un nouveau fichier de script. Copiez le script suivant dans le nouveau fichier de script et cliquez sur **Déboguer** > **exécuter**. Le script suivant vous demandera tout d’abord votre Azure informations d’identification de compte à ajouter votre Azure à l’environnement PowerShell local du compte, puis affichez tous les abonnements qui sont connectés à la session PowerShell locale. Notez le nom de l’abonnement que vous voulez utiliser, en suivant ce didacticiel :

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b. Pour localiser et copier le nom de votre abonnement dans le [Portail Azure](https://portal.azure.com), dans le menu concentrateur sur la gauche, cliquez sur **abonnements**. Copier le nom d’abonnement que vous souhaitez utiliser lors de l’exécution des scripts dans ce guide.

        ![Portail Azure][Image2]

        c. Pour localiser et copier votre nom de l’abonnement dans le [Portail classique Azure](https://manage.windowsazure.com/), faites défiler vers le bas, puis cliquez sur **paramètres** sur le côté gauche du portail. Cliquez sur **abonnements** pour afficher la liste de vos abonnements. Copier le nom d’abonnement que vous souhaitez utiliser lors de l’exécution des scripts qui figurent dans ce guide.

        ![Portail classique Azure][Image1]

    - **$StorageAccountName :** Utilisez le prénom dans le script ou entrez un nouveau nom pour votre compte de stockage. **Importantes :** Le nom du compte de stockage doit être unique dans Azure. Il doit être en minuscule, trop !

    - **$Location :** Utilisez la donnée « États-Unis Ouest » dans le script ou sélectionnez d’autres emplacements Azure, tels que les États-Unis, Europe du Nord et ainsi de suite.

    - **$ContainerName :** Utilisez le prénom dans le script ou entrez un nouveau nom pour votre conteneur.

    - **$ImageToUpload :** Entrez un chemin d’accès à une image sur votre ordinateur local, tel que : « C:\Images\HelloWorld.png ».

    - **$DestinationFolder :** Entrez le chemin d’un répertoire local pour stocker les fichiers téléchargés à partir du stockage Azure, telles que : « C:\DownloadImages ».

7.  Après mise à jour les variables de script dans le fichier « mystoragescript.ps1 », cliquez sur **fichier** > **Enregistrer**. Ensuite, cliquez sur **Déboguer** > **exécuter** ou appuyez sur **F5** pour exécuter le script.  

Une fois que le script s’exécute, vous devez disposer d’un dossier de destination local qui inclut le fichier image téléchargée. La capture d’écran suivante montre un exemple de résultat :

![Télécharger des objets BLOB][Image3]


> [AZURE.NOTE] La section « Mise en route avec stockage Azure et PowerShell dans 5 minutes » fourni une brève introduction sur l’utilisation de PowerShell Azure avec stockage Azure. Pour plus d’informations et des instructions, nous vous invitons à lire les sections suivantes.

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Conditions préalables pour l’utilisation de PowerShell Azure avec stockage Azure
Vous avez besoin d’un abonnement Azure et un compte pour exécuter les applets de commande PowerShell décrite dans ce guide, comme décrit ci-dessus.

PowerShell Azure est un module qui fournit des applets de commande pour gérer Azure à utiliser Windows PowerShell. Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Nous vous recommandons de télécharger et installer ou mettre à niveau vers le dernier module Azure PowerShell avant d’utiliser ce guide.

Vous pouvez exécuter les applets de commande dans la console Windows PowerShell standard ou le Windows PowerShell l’écriture de script environnement intégré (ISE). Par exemple, pour ouvrir **Windows PowerShell ISE**, accédez au menu Démarrer, tapez les outils d’administration et cliquez sur Exécuter. Dans la fenêtre Outils d’administration, avec le bouton droit de Windows PowerShell ISE, cliquez sur Exécuter en tant qu’administrateur.

## <a name="how-to-manage-storage-accounts-in-azure"></a>Comment gérer les comptes de stockage dans Azure

### <a name="how-to-set-a-default-azure-subscription"></a>Comment définir une valeur par défaut abonnement Azure
Pour gérer le stockage Azure à l’aide de PowerShell Azure, vous devez authentifier votre environnement client Azure via l’authentification Azure Active Directory ou l’authentification basée sur le certificat. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) didacticiel. Ce guide utilise l’authentification Azure Active Directory.

1.  Dans Windows PowerShell ISE, tapez la commande suivante pour ajouter votre Azure du compte à l’environnement local PowerShell :

    `Add-AzureAccount`

2.  Dans la fenêtre « Se dans à Microsoft Azure », tapez l’adresse de messagerie et le mot de passe associés à votre compte. Azure authentifie enregistre les informations d’identification et puis ferme la fenêtre.

3.  Ensuite, exécutez la commande suivante pour afficher les comptes Azure dans votre environnement local de PowerShell et vérifiez que votre compte est répertorié :

    `Get-AzureAccount`

4.  Ensuite, exécutez l’applet de commande suivante pour afficher tous les abonnements qui sont connectés à la session PowerShell locale et vérifiez que votre abonnement apparaît :

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  Pour définir un abonnement Azure par défaut, exécutez l’applet de commande Sélectionner AzureSubscription :

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  Vérifiez le nom de l’abonnement par défaut en exécutant l’applet de commande Get-AzureSubscription :

    `Get-AzureSubscription -Default`

7.  Pour voir toutes les cmdlets PowerShell disponibles pour le stockage Azure, exécutez :

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>Comment créer un nouveau compte de stockage Azure
Pour utiliser le stockage Azure, vous avez besoin d’un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour vous connecter à votre abonnement.

1.  Exécutez l’applet de commande Get-AzureLocation pour rechercher tous les emplacements de centre de données disponibles :

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  Ensuite, exécutez l’applet de commande New-AzureStorageAccount pour créer un nouveau compte de stockage. L’exemple suivant crée un nouveau compte de stockage du centre de données « US ouest ».

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] Le nom de votre compte de stockage doit être unique dans Azure et doit être en minuscule. Pour des conventions et des restrictions, voir [à propos des comptes d’espace de stockage Azure](storage-create-storage-account.md) et [appellation et faisant référence à des conteneurs, des objets BLOB et les métadonnées](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### <a name="how-to-set-a-default-azure-storage-account"></a>Comment faire pour configurer un compte de stockage Azure par défaut
Vous pouvez avoir plusieurs comptes de stockage dans votre abonnement. Vous pouvez choisir un d’eux et définissez-le comme le compte de stockage par défaut pour toutes les commandes de stockage dans la même session PowerShell. Cela vous permet d’exécuter les commandes de stockage Azure PowerShell sans spécifier explicitement le contexte de stockage.

1.  Pour définir un compte de stockage par défaut pour votre abonnement, vous pouvez exécuter l’applet de commande Set-AzureSubscription.

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  Ensuite, exécutez l’applet de commande Get-AzureSubscription pour vérifier que le compte de stockage est associé à votre compte d’abonnement par défaut. Cette commande renvoie les propriétés d’un abonnement de l’abonnement actuel, y compris son compte de stockage en cours.

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Comment obtenir la liste tous les comptes de stockage Azure dans un abonnement
Chaque abonnement Azure peut avoir jusqu'à 100 comptes de stockage. Pour obtenir les dernières informations sur les limites, voir [abonnement Azure et limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md).

Exécutez l’applet de commande suivante pour déterminer le nom et l’état des comptes de stockage dans l’abonnement actif :

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>Comment créer un contexte de stockage Azure
Contexte de stockage Azure est un objet dans PowerShell pour encapsuler les informations d’identification de stockage. En utilisant un contexte de stockage pendant toute applet de commande suivante en cours d’exécution vous permet de s’authentifier votre demande sans spécification du compte de stockage et sa touche d’accès rapide explicite. Vous pouvez créer un contexte de stockage de plusieurs façons, par exemple à l’aide de la clé accès et le nom de compte de stockage, jeton de signature (sa) accès partagé, chaîne de connexion, ou anonymes. Pour plus d’informations, voir [AzureStorageContext de nouveau](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Utilisez une des manières suivantes pour créer un contexte de stockage :

- Exécutez l’applet de commande [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) pour déterminer la touche d’accès de stockage principal pour votre compte de stockage Azure. Vous devez ensuite appeler l’applet de commande [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) pour créer un contexte de stockage :

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Générer un jeton de signature accès partagé pour un conteneur de stockage Azure et utilisez-le pour créer un contexte de stockage :

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    Pour plus d’informations, voir [Nouveau AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) et [à l’aide de partagés accès Signatures (sa)](storage-dotnet-shared-access-signature-part-1.md).

- Dans certains cas, vous souhaiterez spécifier les points de terminaison de service lorsque vous créez un nouveau contexte de stockage. Cela peut être nécessaire lorsque vous avez enregistré un nom de domaine personnalisé pour votre compte de stockage avec le service Blob ou vous voulez utiliser une signature accès partagé pour accéder à des ressources de stockage. Définir les points de terminaison de service dans une chaîne de connexion et utilisez-le pour créer un nouveau contexte de stockage comme indiqué ci-dessous :

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Pour plus d’informations sur la configuration d’une chaîne de connexion de stockage, voir [Configuration des chaînes de connexion](storage-configure-connection-string.md).

À présent que vous avez configuré votre ordinateur et appris à gérer les abonnements et les comptes de stockage à l’aide de PowerShell Azure, accédez à la section suivante pour apprendre à gérer des objets BLOB Azure et blob instantanés.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Comment récupérer ou de régénérer les clés de stockage Azure

Un compte de stockage Azure est fourni avec deux clés de compte. Vous pouvez utiliser l’applet de commande suivante pour récupérer vos clés.

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

Utiliser l’applet de commande suivante pour récupérer une clé spécifique. Les valeurs valides sont les principales et secondaires.  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

Si vous voulez régénérer vos clés, utilisez l’applet de commande suivante. Valeurs valides pour le type de clé - sont « Principale » et « Secondaire »

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>Comment gérer les objets BLOB Azure
Stockage d’objets Blob Azure est un service de stockage de grandes quantités de données non structurées, tels que du texte ou des données binaires, qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts de Service de stockage d’objets Blob Azure. Pour plus d’informations, voir [prise en main stockage d’objets Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) et [Concepts de Service Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Comment créer un conteneur
Chaque blob dans le stockage Azure doit être placé dans un conteneur. Vous pouvez créer un conteneur privé à l’aide de l’applet de commande New-AzureStorageContainer :

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Il existe trois niveaux d’accès anonyme en lecture : **désactiver** **Blob**et **conteneur**. Pour empêcher tout accès anonyme aux objets BLOB, définissez le paramètre d’autorisation à **désactiver**. Par défaut, le nouveau conteneur est privé et sont accessibles uniquement par le propriétaire du compte. Pour autoriser l’accès en lecture public anonyme blob de ressources, mais pas pour les métadonnées de conteneur ou à la liste des objets BLOB dans le conteneur, définissez le paramètre d’autorisation sur **Blob**. Pour autoriser l’accès en lecture publique complète aux ressources blob, conteneur métadonnées et la liste des objets BLOB dans le conteneur, définissez le paramètre d’autorisation au **conteneur**. Pour plus d’informations, voir [Gérer les accès en lecture anonymes à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md).

### <a name="how-to-upload-a-blob-into-a-container"></a>Comment télécharger un blob dans un conteneur
Stockage d’objets Blob Azure prend en charge des objets BLOB de page et les objets BLOB bloc. Pour plus d’informations, voir [présentation bloc BLOB, ajouter des objets BLOB et des objets BLOB de Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Pour télécharger des objets BLOB dans un conteneur, vous pouvez utiliser l’applet de commande [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) . Par défaut, cette commande transfère les fichiers locaux à un blob bloc. Pour spécifier le type de l’objet blob, vous pouvez utiliser le paramètre - BlobType.

L’exemple suivant s’exécute l’applet de commande [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) pour accéder à tous les fichiers dans le dossier spécifié et les transmet à l’applet de commande suivante en utilisant l’opérateur de pipeline. L’applet de commande [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) télécharge les fichiers locaux vers votre conteneur :

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>Comment télécharger des objets BLOB à partir d’un conteneur
L’exemple suivant montre comment télécharger des objets BLOB à partir d’un conteneur. Tout d’abord, l’exemple établit une connexion au stockage Azure à l’aide du contexte de compte de stockage, qui inclut le nom de compte de stockage et sa clé primaire access. Ensuite, l’exemple récupère une référence blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) . Ensuite, l’exemple utilise l’applet de commande [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) pour télécharger des objets BLOB dans le dossier local de destination.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Comment copier des objets BLOB à partir d’un seul conteneur de stockage à un autre
Vous pouvez copier des objets BLOB dans plusieurs zones et des comptes de stockage asynchrone. L’exemple suivant montre comment copier des objets BLOB à partir d’un seul conteneur de stockage à l’autre dans les deux comptes de stockage différents. L’exemple définit tout d’abord les variables de comptes de stockage source et de destination, puis crée un contexte de stockage pour chaque compte. Ensuite, l’exemple de copie des objets BLOB du conteneur source au conteneur de destination à l’aide de l’applet de commande [Démarrer AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) . L’exemple part du principe que les comptes de stockage source et de destination et conteneurs existent déjà.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Notez que cet exemple exécute une copie asynchrone. Vous pouvez surveiller l’état de chaque copie en exécutant l’applet de commande [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Comment copier des objets BLOB à partir d’un emplacement secondaire
Vous pouvez copier des objets BLOB à partir de l’emplacement d’un compte RA GRS activés secondaire.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>Comment supprimer un blob
Pour supprimer un blob, commencez par obtenir une référence blob, puis appelez l’applet de commande Supprimer AzureStorageBlob dessus. L’exemple suivant supprime tous les objets BLOB dans un conteneur donné. L’exemple définit tout d’abord les variables d’un compte de stockage, puis crée un contexte de stockage. Ensuite, l’exemple récupère une référence blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute l’applet de commande [AzureStorageBlob de supprimer](http://msdn.microsoft.com/library/azure/dn806399.aspx) pour supprimer des objets BLOB à partir d’un conteneur dans le stockage Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>Comment gérer des instantanés blob Azure
Azure vous permet de créer un instantané d’un objet blob. Un instantané est une version en lecture seule d’un blob qui est considérée à un point dans le temps. Une fois un instantané a été créé, il peut être lue, copié, ou supprimé, mais pas modifiée. Instantanés permettent de sauvegarder un blob tel qu’il apparaît à un moment donné. Pour plus d’informations, voir [Création d’un instantané d’un objet Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Comment créer un instantané d’objets blob
Pour créer un instantané d’un objet blob, obtenez d’abord une référence blob, puis appelez la `ICloudBlob.CreateSnapshot` méthode dessus. L’exemple suivant définit les variables d’un compte de stockage en premier, puis crée un contexte de stockage. Ensuite, l’exemple récupère une référence blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute la méthode [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) pour créer une capture instantanée.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>Comment obtenir la liste des instantanés d’un blob
Vous pouvez créer des instantanés autant que vous le souhaitez pour un blob. Vous pouvez répertorier les instantanés associés à votre blob pour effectuer le suivi de vos instantanés en cours. L’exemple suivant utilise un blob prédéfini et appelle l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) pour répertorier les instantanés de ce blob.  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Comment copier un instantané d’un objet blob
Vous pouvez copier un instantané d’un objet blob de restaurer l’instantané. Pour plus d’informations et des restrictions, voir [Création d’un instantané d’un objet Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). L’exemple suivant définit les variables d’un compte de stockage en premier, puis crée un contexte de stockage. Ensuite, l’exemple définit les variables de nom conteneur et blob. L’exemple récupère une référence blob à l’aide de l’applet de commande [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) et exécute la méthode [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) pour créer une capture instantanée. Ensuite, l’exemple exécute l’applet de commande [Démarrer AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) pour copier l’instantané d’un objet blob à l’aide de l’objet ICloudBlob pour le blob source. Veillez à mettre à jour les variables en fonction de votre configuration avant l’exécution de l’exemple. Notez que l’exemple suivant suppose que la source et les conteneurs de destination et le blob source existent déjà.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

À présent que vous avez appris à gérer des objets BLOB Azure et blob instantanés avec Azure PowerShell, accédez à la section suivante pour apprendre à gérer des tables, des files d’attente et fichiers.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Comment gérer les tables Azure et entités de table
Service de stockage de Table Azure est un magasin de données NoSQL, que vous pouvez utiliser pour stocker et considérables ensembles de données structurées et non relationnelles de la requête. Les principaux composants du service sont des tables, les entités et les propriétés. Une table est un ensemble d’entités. Une entité est un ensemble de propriétés. Chaque entité peut avoir jusqu'à 252 propriétés, qui sont toutes les paires nom-valeur. Cette section suppose que vous êtes déjà familiarisé avec les concepts de Service de stockage de Table Azure. Pour plus d’informations, voir [Présentation du modèle de données de Service Table](http://msdn.microsoft.com/library/azure/dd179338.aspx) et [commencer à utiliser le stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md).

Dans les sous-sections suivantes, vous allez apprendre à gérer le service de stockage de Table Azure à l’aide de PowerShell Azure. Les scénarios présentés incluent **Création**, la **suppression**et la **récupération des** **tables**, ainsi que **Ajout**, **l’interrogation**et **la suppression des entités de table**.

### <a name="how-to-create-a-table"></a>Comment créer un tableau
Chaque table doit se trouver dans un compte de stockage Azure. L’exemple suivant montre comment créer une table dans le stockage Azure. Tout d’abord, l’exemple établit une connexion au stockage Azure à l’aide du contexte de compte de stockage, qui inclut le nom de compte de stockage et sa touche d’accès rapide. Ensuite, elle utilise l’applet de commande [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) pour créer une table dans le stockage Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>Comment récupérer une table
Vous pouvez interroger et récupérer une ou toutes les tables dans un compte de stockage. L’exemple suivant montre comment récupérer une table à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) .

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Si vous appelez l’applet de commande Get-AzureStorageTable sans paramètres, il obtient toutes les tables de stockage pour un compte de stockage.

### <a name="how-to-delete-a-table"></a>Comment supprimer un tableau
Vous pouvez supprimer un tableau à partir d’un compte de stockage à l’aide de l’applet de commande [Supprimer AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) .  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>Comment gérer les entités de table
Pour l’instant, Azure PowerShell ne fournit pas les applets de commande pour gérer les entités de table directement. Pour effectuer les opérations sur les entités de table, vous pouvez utiliser les classes qui figurent dans la [Bibliothèque de Client de stockage Azure pour .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Comment ajouter des entités de table
Pour ajouter une entité à une table, tout d’abord créer un objet qui définit les propriétés de votre entité. Une entité peut avoir jusqu'à 255 propriétés, y compris les 3 Propriétés système : **PartitionKey**, **RowKey**et **horodatage**. Vous êtes responsable de l’insertion et mise à jour les valeurs de **PartitionKey** et **RowKey**. Le serveur gère la valeur d' **horodatage**, qui ne peut pas être modifiée. Ensemble la **PartitionKey** et **RowKey** identifient chaque entité au sein d’un tableau.

-   **PartitionKey**: détermine la partition stockée dans l’entité.
-   **RowKey**: identifie de façon unique l’entité au sein de la partition.

Vous pouvez définir jusqu'à 252 propriétés personnalisées pour une entité. Pour plus d’informations, voir [Présentation du modèle de données de Service Table](http://msdn.microsoft.com/library/azure/dd179338.aspx).

L’exemple suivant montre comment ajouter des entités à un tableau. L’exemple montre comment récupérer la table employés et ajoutez plusieurs entités dedans. Tout d’abord, il connecte au stockage Azure à l’aide du contexte de compte de stockage, qui inclut le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il récupère la table spécifiée à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Si la table n’existe pas, l’applet de commande [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) est utilisé pour créer une table dans le stockage Azure. Ensuite, l’exemple définit une fonction personnalisée entité Ajouter pour ajouter des entités à la table en spécifiant partition de chaque entité et la clé de ligne. La fonction Ajouter entité appelle l’applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) sur la classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) pour créer un objet entité. Une version ultérieure, l’exemple appelle la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) sur cet objet entité pour l’ajouter à une table.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>Comment faire pour interroger entités de table
Pour interroger un tableau, utilisez la classe [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) . L’exemple suivant suppose que vous avez déjà exécuté le script décrite dans la procédure pour ajouter une section entités de ce guide. Tout d’abord, l’exemple établit une connexion au stockage Azure à l’aide du contexte de stockage, qui inclut le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il tente d’en récupérer la table « Employés » précédemment créée à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Appel de l’applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) sur la classe Microsoft.WindowsAzure.Storage.Table.TableQuery crée un nouvel objet de requête. L’exemple de recherche pour les entités qui possède une colonne 'ID' dont la valeur est 1 comme indiqué dans un filtre de chaîne. Pour plus d’informations, voir [entités et requêtes des Tables](http://msdn.microsoft.com/library/azure/dd894031.aspx). Lorsque vous exécutez cette requête, elle renvoie toutes les entités qui correspondent aux critères de filtre.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>Comment supprimer des entités de table
Vous pouvez supprimer une entité à l’aide de ses clés partition et ligne. L’exemple suivant suppose que vous avez déjà exécuté le script décrite dans la procédure pour ajouter une section entités de ce guide. Tout d’abord, l’exemple établit une connexion au stockage Azure à l’aide du contexte de stockage, qui inclut le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il tente d’en récupérer la table « Employés » précédemment créée à l’aide de l’applet de commande [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Si la table existe, l’exemple appelle la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) pour récupérer une entité en fonction de ses valeurs clés partition et ligne. Passer ensuite l’entité à la méthode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) à supprimer.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Comment gérer les files d’attente Azure et messages file d’attente
Stockage de file d’attente Azure est un service pour stocker un grand nombre de messages qui sont accessibles à partir de n’importe où dans le monde via des appels authentifiés à l’aide de HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts de Service de stockage Azure file d’attente. Pour plus d’informations, voir [prise en main stockage Azure file d’attente à l’aide de .NET](storage-dotnet-how-to-use-queues.md).

Cette section vous montrent comment gérer le service de stockage Azure file d’attente à l’aide de PowerShell Azure. Les scénarios présentés incluent **Insertion** et **la suppression** des messages de file d’attente, ainsi que **Création**, **suppression**et **extraction files d’attente**.

### <a name="how-to-create-a-queue"></a>Comment créer une file d’attente
Tout d’abord, l’exemple suivant établit une connexion au stockage Azure à l’aide du contexte de compte de stockage, qui inclut le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il appelle applet de commande [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) pour créer une file d’attente nommée « nom ».

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Pour plus d’informations sur les conventions d’attribution de nom pour le Service de file d’attente Azure, voir [affectation des noms files d’attente et les métadonnées](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Comment récupérer une file d’attente
Vous pouvez interroger et récupérer une file d’attente spécifique ou une liste de toutes les files d’attente dans un compte de stockage. L’exemple suivant montre comment récupérer une file d’attente spécifiée à l’aide de l’applet de commande [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) .

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Si vous appelez l’applet de commande [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sans paramètres, il reçoit une liste de toutes les files d’attente.

### <a name="how-to-delete-a-queue"></a>Comment supprimer une file d’attente
Pour supprimer une file d’attente et tous les messages qu’elle contient, contactez l’applet de commande Supprimer AzureStorageQueue. L’exemple suivant montre comment supprimer une file d’attente spécifiée à l’aide de l’applet de commande Supprimer AzureStorageQueue.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>Comment insérer un message dans une file d’attente
Pour insérer un message dans une file d’attente existante, tout d’abord créer une nouvelle instance de la classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Ensuite, appelez la méthode [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Un CloudQueueMessage peut être créé à partir d’une chaîne (au format UTF-8) ou un tableau d’octets.

L’exemple suivant montre comment ajouter le message à une file d’attente. Tout d’abord, l’exemple établit une connexion au stockage Azure à l’aide du contexte de compte de stockage, qui inclut le nom de compte de stockage et sa touche d’accès rapide. Ensuite, il récupère la file d’attente spécifiée à l’aide de l’applet de commande [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) . Si la file d’attente existe, l’applet de commande [New-Object](http://technet.microsoft.com/library/hh849885.aspx) est utilisé pour créer une instance de la classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Une version ultérieure, l’exemple appelle la méthode [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) sur cet objet du message pour l’ajouter à une file d’attente. Voici le code qui Récupère une file d’attente et insère le message « MessageInfo » :

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>Comment file d’attente le message suivant
Votre code files d’attente désélectionnez les messages provenant d’une file d’attente en deux étapes. Lorsque vous appelez la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) , vous obtenez le message suivant dans une file d’attente. Un message retourné par **GetMessage** devienne invisible à n’importe quel autre code lire des messages de cette file d’attente. Pour terminer la suppression du message de la file d’attente, vous devez également appeler la méthode [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Ce processus en deux étapes de la suppression d’un message garantit que si votre code ne parvient pas à traiter un message en raison de panne logicielle ou matérielle, une autre instance de votre code pouvez obtenir le même message et essayez à nouveau. Votre code appelle **DeleteMessage** droite une fois que le message a été traité.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>Comment gérer les fichiers et les partages de fichiers Azure
Stockage de fichiers Azure offre un stockage partagé pour les applications utilisant le protocole PME standard. Machines virtuelles Microsoft Azure et services cloud peuvent partager des données de fichiers entre les composants d’application via partages montés et en local applications peuvent accéder aux données du fichier dans un partage via l’API de stockage de fichiers ou PowerShell Azure.

Pour plus d’informations sur le stockage de fichiers Azure, voir [prise en main le stockage de fichiers Azure sous Windows](storage-dotnet-how-to-use-files.md) et [L’API REST de Service de fichier](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## <a name="how-to-set-and-query-storage-analytics"></a>Comment définir et analytique de stockage de requête
Vous pouvez utiliser [Azure stockage Analytique](storage-analytics.md) pour recueillir des mesures pour vos comptes de stockage Azure et les données du journal sur les requêtes envoyées à votre compte de stockage. Vous pouvez utiliser des indicateurs de stockage pour surveiller l’état d’un compte de stockage et de stockage journalisation pour diagnostiquer et résoudre les problèmes avec votre compte de stockage.
Par défaut, métriques de stockage n’est pas activée pour vos services de stockage. Vous pouvez activer la surveillance à l’aide du portail Azure ou Windows PowerShell, ou par programme à l’aide de la bibliothèque de stockage client. Journalisation de stockage produit côté serveur et vous permet d’enregistrer les détails pour les demandes réussies et non dans votre compte de stockage. Ces fichiers journaux permettre d’afficher des détails de lire, écrire et supprimer des opérations sur vos tables, files d’attente et des objets BLOB ainsi que les raisons de demandes a échoué.

Pour savoir comment activer et afficher les données métriques de stockage à l’aide de PowerShell, voir [comment activer métriques de stockage à l’aide de PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Pour savoir comment activer et récupérer les données de journalisation de stockage à l’aide de PowerShell, voir [comment activer la journalisation de stockage à l’aide de PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) et [recherche de vos données de journal journalisation de stockage](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Pour plus d’informations sur l’utilisation de métriques de stockage et de stockage journalisation pour résoudre les problèmes de stockage, voir [diagnostic, de surveillance et résolution des problèmes Microsoft Azure stockage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Comment gérer partagés accès Signature (sa) et stratégie d’accès de stockage
Accès partagé signatures sont une partie importante du modèle de sécurité de n’importe quelle application à l’aide de stockage Azure. Ils sont utiles pour fournir des autorisations limitées à votre compte de stockage pour les clients qui ne doivent pas avoir la clé de compte. Par défaut, seul le propriétaire du compte de stockage peut-être accéder à des objets BLOB, les tables et les files d’attente dans ce compte. Si votre service ou l’application doit rendre ces ressources disponibles à d’autres clients sans partager votre touche d’accès rapide, vous disposez de trois options :

- Définir des autorisations d’un conteneur pour autoriser l’accès anonyme en lecture au conteneur et à ses objets BLOB. Ce n’est pas autorisé pour les tables ou files d’attente.
- Utiliser une signature accès partagé qu’accorde les droits d’accès aux conteneurs, des objets BLOB, files d’attente et tables de restreints pour un intervalle de temps spécifique.
- Utiliser une stratégie d’accès stockée pour obtenir un niveau supplémentaire de contrôle sur les signatures accès partagé pour un conteneur ou ses objets BLOB, pour une file d’attente ou pour une table. La stratégie d’accès stockée permet de modifier l’heure de début, date d’expiration ou les autorisations pour une signature, ou pour révoquer après qu’il a été émis.

Une signature accès partagé peut être l’une des deux formes :

- **Associations de sécurité ad hoc**: lorsque vous créez un sa ad hoc, l’heure de début, la date d’expiration, et les autorisations pour les associations de sécurité sont spécifiées dans l’URI SAS. Ce type de sa peut-être être créé dans un conteneur, un blob, un tableau ou file d’attente et il est non revokable.
- **As avec la stratégie d’accès stockée**: une stratégie d’accès stockée est définie sur un conteneur de ressources un conteneur blob, un tableau ou file d’attente - et vous pouvez l’utiliser pour gérer les contraintes pour une ou plusieurs signatures accès partagé. Lorsque vous associez une associations de sécurité à une stratégie d’accès stockées, les associations de sécurité hérite les contraintes - l’heure de début, délai d’expiration et autorisations - définies pour la stratégie d’accès stockée. Ce type de sa est revokable.

Pour plus d’informations, voir [L’aide partagés accès Signatures (sa)](storage-dotnet-shared-access-signature-part-1.md) et [gérer l’accès en lecture anonyme à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md).

Dans les sections suivantes, vous allez apprendre à créer une stratégie d’accès jetons et stockées signature accès partagé pour les tableaux Azure. Azure PowerShell fournit des applets de commande similaire pour conteneurs et des objets BLOB également files d’attente. Pour exécuter les scripts dans cette section, téléchargez le [Azure PowerShell version 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) ou version ultérieure.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Comment créer un jeton de Signature de Access partagés basée sur des règles
Utiliser l’applet de commande New-AzureStorageTableStoredAccessPolicy pour créer une stratégie d’accès stockée. Ensuite, appelez l’applet de commande [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) pour créer un nouveau jeton de signature accès partagé stratégie pour une table de stockage Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>Comment créer un jeton de Signature de Access partagé (non revokable) ad hoc
Utiliser l’applet de commande [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) pour créer un nouveau jeton de Signature d’accès partagé de (non revokable) ad hoc pour un tableau de stockage Azure :

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>Comment créer une stratégie d’accès stockée
Utiliser l’applet de commande New-AzureStorageTableStoredAccessPolicy pour créer une stratégie d’accès stockée pour une table de stockage Azure :

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>Comment mettre à jour une stratégie d’accès stockée
Utiliser l’applet de commande Set-AzureStorageTableStoredAccessPolicy pour mettre à jour une stratégie d’accès stockées existante pour un tableau de stockage Azure :

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>Comment supprimer une stratégie d’accès stockée
Utiliser l’applet de commande Supprimer AzureStorageTableStoredAccessPolicy pour supprimer une stratégie d’accès stockées dans un tableau de stockage Azure :

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Comment utiliser le stockage Azure pour le gouvernement des États-Unis et Azure Chine
Un environnement Azure est un déploiement indépendant de Microsoft Azure, tels que [Pour le gouvernement Azure pour le gouvernement des États-Unis](https://azure.microsoft.com/features/gov/)et [AzureCloud pour Azure global](https://portal.azure.com) [AzureChinaCloud pour Azure géré par 21Vianet en Chine](http://www.windowsazure.cn/). Vous pouvez déployer de nouveaux environnements Azure pour le gouvernement des États-Unis et Azure Chine.

Pour utiliser le stockage Azure avec AzureChinaCloud, vous devez créer un contexte de stockage associé à AzureChinaCloud. Suivez ces étapes pour vous aider :

1.  Exécutez l’applet de commande [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) pour afficher les environnements Azure disponibles :

    `Get-AzureEnvironment`

2.  Ajouter un compte Azure Chine dans Windows PowerShell :

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  Créer un contexte de stockage pour un compte AzureChinaCloud :

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Pour utiliser le stockage Azure avec [Azure américaine](https://azure.microsoft.com/features/gov/), définissez un nouvel environnement et puis créer un nouveau contexte de stockage avec cet environnement :

1.  Exécutez l’applet de commande [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) pour afficher les environnements Azure disponibles :

    `Get-AzureEnvironment`

2.  Ajouter un compte pour le gouvernement Azure nous dans Windows PowerShell :

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  Créer un contexte de stockage pour un compte AzureUSGovernment :

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

Pour plus d’informations, voir :

- [Guide du développeur pour le gouvernement Microsoft Azure](../azure-government-developer-guide.md).
- [Vue d’ensemble des différences lors de la création d’une Application Service Chine](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide, vous avez appris à gérer le stockage Azure avec Azure PowerShell. Voici quelques articles connexes et ressources pour en savoir plus sur les.

- [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Applets de commande PowerShell stockage Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Guide de référence de Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
