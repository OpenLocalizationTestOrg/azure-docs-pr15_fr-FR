<properties
    pageTitle="À l’aide de l’infrastructure du langage commun Azure avec stockage Azure | Microsoft Azure"
    description="Découvrez comment utiliser l’Interface de ligne Azure (commande Azure) avec le stockage Azure pour créer et gérer les comptes de stockage et utiliser des fichiers et des objets BLOB Azure. L’infrastructure du langage commun Azure est un outil de disponibilité sur plusieurs plateformes "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>À l’aide de l’infrastructure du langage commun Azure avec stockage Azure

## <a name="overview"></a>Vue d’ensemble

L’infrastructure du langage commun Azure constitue un ensemble d’open source et disponibilité sur plusieurs plateformes commandes sur l’utilisation de la plateforme Azure. Il fournit la plupart des mêmes fonctionnalités figurant dans le [portail Azure](https://portal.azure.com) , ainsi que les fonctionnalités d’accès aux données élaborés.

Dans ce guide, nous allons découvrir comment utiliser [Azure de ligne de commande Interface de Azure](../xplat-cli-install.md) pour effectuer diverses tâches de développement et d’administration avec le stockage Azure. Nous vous recommandons de télécharger et installer ou mettre à niveau vers la dernière infrastructure du langage commun Azure avant d’utiliser ce guide.

Ce guide suppose que vous comprenez les concepts de base de stockage Azure. Le guide fournit un certain nombre de scripts pour illustrer l’utilisation de l’infrastructure du langage commun Azure avec stockage Azure. Veillez à mettre à jour les variables de script en fonction de votre configuration avant l’exécution de chaque script.

> [AZURE.NOTE] Le guide fournit les exemples de commande et de script Azure infrastructure du langage commun pour les comptes de stockage classique. Reportez-vous [à l’aide de l’infrastructure du langage commun Azure pour Mac, Linux et Windows Azure Gestion des ressources](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) pour Azure infrastructure du langage commun des commandes pour les comptes de stockage Gestionnaire de ressources.

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Prise en main le stockage Azure et de l’infrastructure du langage commun Azure dans 5 minutes

Ce guide utilise Ubuntu pour obtenir des exemples, mais d’autres plateformes de système d’exploitation doivent exécuter de la même manière.

**Aux Azure :** Obtenir un abonnement Microsoft Azure et un compte Microsoft associé à cet abonnement. Pour plus d’informations sur les options d’achat Azure, voir [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/), [Options d’achat](https://azure.microsoft.com/pricing/purchase-options/)et [Propose des membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network, BizSpark et des autres programmes Microsoft).

Pour plus d’informations sur les abonnements Azure, voir [affectation de rôles administrateur dans Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Après avoir créé un abonnement Microsoft Azure et un compte :**

1. Téléchargez et installez l’infrastructure du langage commun Azure suivant les instructions fournies dans [installer l’infrastructure du langage commun Azure](../xplat-cli-install.md).
2. Une fois que l’infrastructure du langage commun Azure a été installé, vous ne pourrez pas utiliser la commande azure à partir de votre interface de ligne de commande (Bash, terminaux, invite) pour accéder aux commandes d’Azure infrastructure du langage commun. Type de `azure` commande et que vous devriez voir le résultat suivant.

    ![Résultat de la commande Azure][Image1]

3. Dans l’interface de ligne de commande, tapez `azure storage` pour répertorier toutes les commandes de stockage azure et obtenir une première impression des fonctionnalités de l’infrastructure du langage commun Azure fournit. Vous pouvez taper le nom de la commande avec le paramètre **-h** (par exemple, `azure storage share create -h`) pour afficher les détails de la syntaxe de la commande.
4. À présent, nous allons vous offre un script simple qui affiche les commandes de base Azure infrastructure du langage commun pour accéder au stockage Azure. Le script demandera tout d’abord vous permet de définir deux variables pour votre compte de stockage et la clé. Puis, le script crée un nouveau conteneur dans ce nouveau compte de stockage et télécharger un fichier image existant (blob) pour qu’ils contiennent. Une fois le script répertorie tous les objets BLOB dans ce conteneur, il télécharge le fichier image au répertoire de destination qui se trouve sur l’ordinateur local.

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. Sur votre ordinateur local, ouvrez votre éditeur de texte par défaut (vim par exemple). Tapez le script ci-dessus dans votre éditeur de texte.

6. À présent, vous devez mettre à jour les variables de script en fonction de vos paramètres de configuration.

    - **< storage_account_name >** Utilisez le prénom dans le script ou entrez un nouveau nom pour votre compte de stockage. **Importantes :** Le nom du compte de stockage doit être unique dans Azure. Il doit être en minuscule, trop !

    - **< storage_account_key >** Touche d’accès rapide de votre compte de stockage.

    - **< container_name >** Utilisez le prénom dans le script ou entrez un nouveau nom pour votre conteneur.

    - **< image_to_upload >** Entrez un chemin d’accès à une image sur votre ordinateur local, tel que : « ~ / images/HelloWorld.png ».

    - **< destination_folder >** Entrez le chemin d’un répertoire local pour stocker les fichiers téléchargés à partir du stockage Azure, telles que : « ~/downloadImages ».

7. Une fois que vous avez mis à jour les variables nécessaires dans vim, appuyez sur les combinaisons de touches « ÉCHAP, :, wq ! » Pour enregistrer le script.

8. Pour exécuter ce script, simplement taper le nom de fichier de script dans la console bash. Une fois que ce script s’exécute, vous devez disposer d’un dossier de destination local qui inclut le fichier image téléchargée. La capture d’écran suivante montre un exemple de résultat :

Une fois que le script s’exécute, vous devez disposer d’un dossier de destination local qui inclut le fichier image téléchargée.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gérer les comptes de stockage avec l’infrastructure du langage commun Azure

### <a name="connect-to-your-azure-subscription"></a>Se connecter à votre abonnement Azure

Bien que la plupart des commandes stockage fonctionnent sans abonnement Azure, nous vous recommandons de vous connecter à votre abonnement depuis l’interface Azure. Pour configurer l’infrastructure du langage commun Azure pour l’utiliser avec votre abonnement, suivez les étapes de [se connecter à un abonnement Azure depuis l’interface Azure](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Créer un nouveau compte de stockage

Pour utiliser le stockage Azure, vous avez besoin d’un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour vous connecter à votre abonnement.

        azure storage account create <account_name>

Le nom de votre compte de stockage doit être comprise entre 3 et 24 caractères et utiliser des nombres et les lettres minuscules uniquement.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Configurer un compte de stockage Azure par défaut dans les variables d’environnement

Vous pouvez avoir plusieurs comptes de stockage dans votre abonnement. Vous pouvez choisir un d’eux et définissez-le dans les variables d’environnement pour toutes les commandes de stockage dans la même session. Cela vous permet d’exécuter les commandes de stockage Azure infrastructure du langage commun sans spécifier le compte de stockage et de clé explicitement.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Autre procédure pour configurer un compte de stockage par défaut est à l’aide de chaîne de connexion. Tout d’abord obtenir la chaîne de connexion en commande :

        azure storage account connectionstring show <account_name>

Puis copiez la chaîne de connexion de sortie et le configurer pour la variable d’environnement :

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>Créer et gérer des objets BLOB

Stockage d’objets Blob Azure est un service de stockage de grandes quantités de données non structurées, tels que du texte ou des données binaires, qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts de stockage Blob Azure. Pour plus d’informations, voir [prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) et [Concepts de Service Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Créer un conteneur

Chaque blob dans le stockage Azure doit être placé dans un conteneur. Vous pouvez créer un conteneur privé à l’aide du `azure storage container create` commande :

        azure storage container create mycontainer

> [AZURE.NOTE] Il existe trois niveaux d’accès anonyme en lecture : **désactiver** **Blob**et **conteneur**. Pour empêcher tout accès anonyme aux objets BLOB, définissez le paramètre d’autorisation à **désactiver**. Par défaut, le nouveau conteneur est privé et sont accessibles uniquement par le propriétaire du compte. Pour autoriser l’accès en lecture public anonyme blob de ressources, mais pas pour les métadonnées de conteneur ou à la liste des objets BLOB dans le conteneur, définissez le paramètre d’autorisation sur **Blob**. Pour autoriser l’accès en lecture publique complète aux ressources blob, conteneur métadonnées et la liste des objets BLOB dans le conteneur, définissez le paramètre d’autorisation au **conteneur**. Pour plus d’informations, voir [Gérer les accès en lecture anonymes à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Stockage d’objets Blob Azure prend en charge des objets BLOB de page et les objets BLOB bloc. Pour plus d’informations, voir [présentation bloc BLOB, ajouter des objets BLOB et des objets BLOB de Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Pour télécharger des objets BLOB dans un conteneur, vous pouvez utiliser la `azure storage blob upload`. Par défaut, cette commande transfère les fichiers locaux à un blob bloc. Pour spécifier le type de l’objet blob, vous pouvez utiliser la `--blobtype` paramètre.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>Télécharger des objets BLOB à partir d’un conteneur

L’exemple suivant montre comment télécharger des objets BLOB à partir d’un conteneur.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>Copier des objets BLOB

Vous pouvez copier des objets BLOB au sein ou entre les régions et les comptes de stockage asynchrone.

L’exemple suivant montre comment copier des objets BLOB à partir d’un compte de stockage à l’autre. Dans cet exemple, nous créons un conteneur dans lequel les objets BLOB sont publiquement, accessibles de manière anonyme.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

Cet exemple effectue une copie asynchrone. Vous pouvez surveiller l’état de chaque opération de copie en exécutant la `azure storage blob copy show` opération.

Notez que l’URL source fourni pour l’opération de copie doit être accessibles au public, ou inclure un jeton de sa (signature accès partagé).

### <a name="delete-a-blob"></a>Supprimer un blob

Pour supprimer un blob, utilisez l’en dessous de la commande :

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>Créer et gérer des partages de fichiers

Stockage de fichiers Azure offre un stockage partagé pour les applications utilisant le protocole PME standard. Machines virtuelles Microsoft Azure et les services en nuage, ainsi que les applications en local, peuvent partager des données de fichier via partages montés. Vous pouvez gérer les partages de fichiers et les données du fichier via l’infrastructure du langage commun Azure. Pour plus d’informations sur le stockage de fichiers Azure, voir [prise en main le stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md) ou [l’utilisation de stockage de fichiers Azure avec Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Créer un partage de fichiers

Un partage de fichiers Azure est un partage de fichiers PME dans Azure. Tous les fichiers et répertoires doivent être créés dans un partage de fichiers. Un compte peut contenir un nombre illimité d’actions, puis un partage peut stocker un nombre illimité de fichiers, jusqu'à atteindre les limites de capacité du compte de stockage. L’exemple suivant crée un partage de fichiers nommé **partage**.

        azure storage share create myshare

### <a name="create-a-directory"></a>Créer un répertoire

Un répertoire fournit une structure hiérarchique facultative pour un partage de fichiers Azure. L’exemple suivant crée un répertoire nommé **myDir** dans le partage de fichiers.

        azure storage directory create myshare myDir

Remarque Ce chemin d’accès du répertoire peut inclure plusieurs niveaux, *par exemple*, **un / b**. Toutefois, vous devez vous assurer que tous les répertoires parents existe. Par exemple, pour le chemin d’accès **un / b**, vous devez commencer par créer répertoire **un** , puis créer répertoire **b**.

### <a name="upload-a-local-file-to-directory"></a>Télécharger un fichier local au répertoire

L’exemple suivant télécharge un fichier à partir de **~/temp/samplefile.txt** à l’annuaire **MonRép** . Modifier le chemin d’accès pour qu’il pointe vers un fichier valide sur votre ordinateur local :

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Notez qu’un fichier dans le partage peut être jusqu'à 1 To.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Énumérer les fichiers dans le partage racine ou le répertoire

Vous pouvez énumérer les fichiers et les sous-répertoires dans une racine de partage ou d’un répertoire à l’aide de la commande suivante :

        azure storage file list myshare myDir

Notez que le nom du répertoire est facultatif pour l’opération de la liste. En cas d’omission, la commande répertorie le contenu du répertoire racine du partage.

### <a name="copy-files"></a>Copier des fichiers

Depuis la version 0.9.8 d’Azure infrastructure du langage commun, vous pouvez copier un fichier à un autre fichier, un fichier à un blob ou un objet blob à un fichier. Ci-dessous, nous montrent comment effectuer ces opérations de copie à l’aide des commandes de l’infrastructure du langage commun. Pour copier un fichier vers le nouveau répertoire :

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Pour copier un blob vers un répertoire de fichiers :

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>Étapes suivantes

Voici quelques articles connexes et ressources pour en savoir plus sur le stockage Azure.

- [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Référence des API REST stockage Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
