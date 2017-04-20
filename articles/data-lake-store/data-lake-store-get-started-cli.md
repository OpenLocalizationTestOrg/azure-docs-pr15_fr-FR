<properties
   pageTitle="Prise en main données Lake Store à l’aide d’interface de ligne de commande disponibilité sur plusieurs plateformes | Microsoft Azure"
   description="Ligne de commande de disponibilité sur plusieurs plateformes Azure permet de créer un compte Data Lake Store et effectuer des opérations de base"
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

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Prise en main Azure données Lake Store à l’aide de la ligne de commande Azure

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Azure infrastructure du langage commun](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser Azure interface de ligne de commande pour créer un compte Azure données Lake Store et effectuer des opérations de base telles que la création de dossiers, chargement et téléchargement des fichiers de données, supprimez votre compte, etc.. Pour plus d’informations sur les données Lake Store, voir [Vue d’ensemble de données Lake Store](data-lake-store-overview.md).

L’infrastructure du langage commun Azure est activée dans Node.js. Il peut être utilisé dans n’importe quelle plate-forme qui prend en charge Node.js, y compris Windows, Mac et Linux. L’infrastructure du langage commun Azure est open source. Le code source est géré dans GitHub en <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Cet article traite uniquement à l’aide de l’infrastructure du langage commun Azure avec données Lake Store. Pour un guide général sur l’utilisation Azure infrastructure du langage commun, voir [comment utiliser l’infrastructure du langage commun Azure] [azure-command-line-tools].


##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Azure infrastructure du langage commun** - voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) pour plus d’informations d’installation et de configuration. Vérifiez que vous redémarrez votre ordinateur après avoir installé l’infrastructure du langage commun.

## <a name="authentication"></a>Authentification

Cet article utilise une approche d’authentification plus simple avec données Lake Store où se connecter en tant qu’utilisateur par l’utilisateur final. Le niveau d’accès au magasin Lake des données système de fichiers et de compte est régi puis par le niveau d’accès de l’utilisateur connecté. Toutefois, il existe d’autres approches ainsi que pour vous authentifier avec données Lake Store, qui sont **l’authentification de l’utilisateur final** ou **service à service**. Pour plus d’informations sur la façon de s’authentifier et des instructions, voir [authentifier avec données Lake Store à l’aide de Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

##<a name="login-to-your-azure-subscription"></a>Connexion à votre abonnement Azure

1. Suivez les étapes présentées dans [se connecter à un abonnement Azure à partir de l’Interface de ligne de Azure (commande Azure)](../xplat-cli-connect.md) et se connecter à votre abonnement à l’aide du `azure login` méthode.

2. Les abonnements qui sont associées à votre compte à l’aide de la liste le `azure account list` commande.

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    Dans le résultat ci-dessus, **Azure-sub-1** est activée et l’autre abonnement est **Azure-sub-2**. 

3. Sélectionnez l’abonnement que vous souhaitez utiliser sous. Si vous voulez travailler sur l’abonnement Azure-sub-2, utilisez la `azure account set` commande.

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure données Lake Store

Ouvrez une invite de commande, shell ou une session Terminal Server et exécutez les commandes suivantes.

2. Basculez en mode Azure le Gestionnaire de ressources à l’aide de la commande suivante :

        azure config mode arm


5. Créer un nouveau groupe de ressources. Dans la commande suivante, fournissent les valeurs de paramètre que vous voulez utiliser.

        azure group create <resourceGroup> <location>

    Si le nom d’emplacement contient des espaces, placez-le entre guillemets. Par exemple « Extrême-Orient US 2 ».

5. Créer le compte de données Lake Store.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Créer des dossiers dans votre magasin Lake de données

Vous pouvez créer des dossiers sous votre compte Azure données Lake Store pour gérer et stocker des données. Utilisez la commande suivante pour créer un dossier nommé « MonNouveauDossier » à la racine de la banque Lake de données.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Par exemple :

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Télécharger des données dans votre magasin Lake de données

Vous pouvez télécharger vos données dans données Lake magasin directement à la racine ou dans un dossier que vous avez créée dans le compte. Extraits de code ci-dessous montrent comment télécharger des exemples de données dans le dossier (**MonNouveauDossier**) que vous avez créé dans la section précédente.

Si vous cherchez des exemples de données à télécharger, vous pouvez obtenir le dossier **Ambulance données** à partir du [Référentiel de Git Azure données Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Télécharger le fichier et le stocker dans un répertoire local sur votre ordinateur, par exemple C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Par exemple :

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Liste des fichiers de données Lake Store

Utilisez la commande suivante pour répertorier les fichiers dans un compte de données Lake Store.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Par exemple :

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

Le résultat de cette doit être semblable à ce qui suit :

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renommer, télécharger et supprimer des données à partir de votre magasin Lake de données

* **Pour renommer un fichier**, utilisez la commande suivante :

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Par exemple :

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Pour télécharger un fichier**, utilisez la commande suivante. Assurez-vous que le chemin d’accès de destination que vous spécifiez déjà existe.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Par exemple :

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Pour supprimer un fichier**, utilisez la commande suivante :

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Par exemple :

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Lorsque vous y êtes invité, saisissez **Y** pour supprimer l’élément.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Afficher la liste de contrôle d’accès pour un dossier données Lake Store

Utiliser la commande suivante pour afficher les utilisateurs sur un dossier données Lake Store. Dans la version actuelle, les utilisateurs peuvent être définies uniquement à la racine de la banque Lake de données. Par conséquent, le paramètre de chemin d’accès ci-dessous sera toujours racine (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Par exemple :

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Supprimer votre compte données Lake Store

Utilisez la commande suivante pour supprimer un compte de données Lake Store.

    azure datalake store account delete <dataLakeStoreAccountName>

Par exemple :

    azure datalake store account delete mynewdatalakestore

Lorsque vous y êtes invité, saisissez **Y** pour supprimer le compte.


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
