<properties
   pageTitle="Prise en main données Lake Store | Azure"
   description="Utiliser PowerShell Azure pour créer un compte Data Lake Store et effectuer des opérations de base"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/04/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Prise en main Azure données Lake Store à l’aide de PowerShell Azure

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Azure infrastructure du langage commun](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser PowerShell Azure pour créer un compte Azure données Lake Store et effectuer les opérations de base telles que la création de dossiers, chargement et téléchargement des fichiers de données, supprimez votre compte, etc.. Pour plus d’informations sur les données Lake Store, voir [Vue d’ensemble de données Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

* **Azure PowerShell 1.0 ou version ultérieure**. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="authentication"></a>Authentification

Cet article utilise une approche d’authentification plus simple avec données Lake Store où vous êtes invité à entrer vos informations d’identification de compte Azure. Le niveau d’accès au magasin Lake des données système de fichiers et de compte est régi puis par le niveau d’accès de l’utilisateur connecté. Toutefois, il existe d’autres approches ainsi que pour vous authentifier avec données Lake Store, qui sont **l’authentification de l’utilisateur final** ou **service à service**. Pour plus d’informations sur la façon de s’authentifier et des instructions, voir [authentifier avec données Lake Store à l’aide de Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure données Lake Store

1. À partir de votre bureau, ouvrez une nouvelle fenêtre de Windows PowerShell et entrez l’extrait de code suivante pour vous connecter à votre compte Azure, définir l’abonnement et enregistrer le fournisseur de données Lake Store. Lorsque vous êtes invité à se connecter, vérifiez que vous ouvrez une session dans l’un des admininistrators/propriétaire de l’abonnement :

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Un compte Azure données Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")

2. Créez un compte Azure données Lake Store. Le nom que vous spécifiez doit contenir uniquement les lettres minuscules et numéros.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Créer un compte Azure données Lake Store] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Azure données Lake Store")

3. Vérifiez que le compte est créé.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    La sortie pendant ce doit être **vraie**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Créer des structures de répertoire dans votre magasin Lake de données Azure

Vous pouvez créer des répertoires sous votre compte Azure données Lake Store pour gérer et stocker des données.

1. Spécifier un répertoire racine.

        $myrootdir = "/"

2. Créer un nouveau répertoire nommé **mynewdirectory** sous la racine spécifiée.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Vérifiez que le nouveau répertoire est créé.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Il doit afficher un résultat comme suit :

    ![Vérifier le répertoire] (./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Vérifier le répertoire")


## <a name="upload-data-to-your-azure-data-lake-store"></a>Télécharger des données dans votre magasin Lake de données Azure

Vous pouvez télécharger vos données dans données Lake magasin directement à la racine ou dans un répertoire que vous avez créée dans le compte. Extraits de code ci-dessous montrent comment télécharger des exemples de données dans le répertoire (**mynewdirectory**) que vous avez créé dans la section précédente.

Si vous cherchez des exemples de données à télécharger, vous pouvez obtenir le dossier **Ambulance données** à partir du [Référentiel de Git Azure données Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Télécharger le fichier et le stocker dans un répertoire local sur votre ordinateur, par exemple C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renommer, télécharger et supprimer des données à partir de votre magasin Lake de données

Pour renommer un fichier, utilisez la commande suivante :

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Pour télécharger un fichier, utilisez la commande suivante :

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Pour supprimer un fichier, utilisez la commande suivante :

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Lorsque vous y êtes invité, saisissez **Y** pour supprimer l’élément. Si vous avez plus d’un fichier à supprimer, vous pouvez fournir tous les chemins d’accès séparés par des virgules.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Supprimer votre compte Azure données Lake Store

Utilisez la commande suivante pour supprimer votre compte données Lake Store.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Lorsque vous y êtes invité, saisissez **Y** pour supprimer le compte.


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
