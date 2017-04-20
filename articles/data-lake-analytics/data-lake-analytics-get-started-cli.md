<properties 
   pageTitle="Prise en main Azure données Lake Analytique à l’aide d’Azure de ligne de commande Interface | Microsoft Azure" 
   description="Découvrez comment utiliser l’Interface de ligne de commande Azure pour créer un compte Data Lake Store, créez une tâche de données Lake Analytique à l’aide de U-SQL et soumettre la tâche. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Didacticiel : prise en main Azure données Lake Analytique à l’aide d’Azure Interface de ligne de commande (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Découvrez comment utiliser Azure infrastructure du langage commun pour créer des comptes Azure données Lake Analytique, définir des tâches de données Lake Analytique en [U-SQL](data-lake-analytics-u-sql-get-started.md)et soumettre des travaux aux comptes de données Lake Analytique. Pour plus d’informations sur les données Lake Analytique, voir [vue d’ensemble Azure données Lake Analytique](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer une tâche qui lit un onglet fichier CSV (TSV) et la convertit dans un fichier CSV (valeurs) séparées par des virgules. Pour ouvrir le même didacticiel à l’aide d’autres outils pris en charge, cliquez sur les onglets situés en haut de cette section.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Azure infrastructure du langage commun**. Voir [installer et configurer Azure infrastructure du langage commun](../xplat-cli-install.md).
    - Téléchargez et installez la **version précommerciale** [Azure infrastructure du langage commun outils](https://github.com/MicrosoftBigData/AzureDataLake/releases) pour effectuer cette démonstration.
- **Authentification**, en utilisant la commande suivante :

        azure login
    Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, voir [se connecter à un abonnement Azure depuis l’interface Azure](../xplat-cli-connect.md).
- **Basculer vers le mode directeur des ressources Azure**, en utilisant la commande suivante :

        azure config mode arm
        
## <a name="create-data-lake-analytics-account"></a>Créer le compte de données Lake Analytique

Vous devez disposer un compte données Lake Analytique avant de pouvoir exécuter toutes les tâches. Pour créer un compte de données Lake Analytique, vous devez spécifier les éléments suivants :

- **Groupe de ressources Azure**: A données Lake Analytique compte doit être créé dans un groupe de ressources Azure. [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) permet de vous permettent de travailler avec les ressources dans votre application en tant que groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources pour votre application dans une seule opération coordonnée.  

    Énumérer les groupes de ressources dans votre abonnement :
    
        azure group list 
    
    Pour créer un nouveau groupe de ressources :

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nom du compte Analytique Lake de données**
- **Emplacement**: parmi les centres de données Azure qui prend en charge les données Lake Analytique.
- **Compte par défaut données Lake**: chaque compte données Lake Analytique possède un compte de données Lake par défaut.

    À la liste compte Lake données existant :
    
        azure datalake store account list

    Pour créer un nouveau compte Lake de données :

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] Le nom du compte données Lake doit contenir uniquement les lettres minuscules et numéros.



**Pour créer un compte de données Lake Analytique**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![Afficher les données Lake Analytique compte](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] Le nom du compte données Lake Analytique doit contenir uniquement les lettres minuscules et numéros.


## <a name="upload-data-to-data-lake-store"></a>Télécharger des données au magasin Lake des données

Dans ce didacticiel, vous traitera des journaux de recherche.  Le journal de recherche pouvant être stocké dans le magasin de données Lake ou stockage d’objets Blob Azure. 

Le portail Azure fournit une interface utilisateur de copie des fichiers d’exemples de données au compte Lake de données par défaut, qui incluent un fichier journal de recherche. Voir [préparer les données source](data-lake-analytics-get-started-portal.md#prepare-source-data) pour télécharger les données sur le compte de données Lake Store par défaut.

Pour télécharger des fichiers à l’aide d’infrastructure du langage commun, utilisez la commande suivante :

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Données Lake Analytique peut également accéder à stockage d’objets Blob Azure.  Pour télécharger des données vers le stockage Blob Azure, voir [à l’aide de l’infrastructure du langage commun Azure avec stockage Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Soumettre les tâches de données Lake Analytique

Les tâches de données Lake Analytique sont écrits en langage SQL-U. Pour en savoir plus sur U-SQL, voir [prise en main U-SQL langue](data-lake-analytics-u-sql-get-started.md) et [référence du langage SQL-U](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer un script de travail données Lake Analytique**

- Créer un fichier texte contenant script U-SQL suivant et enregistrez le fichier texte sur votre poste de travail :

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Ce script U SQL lit le fichier de données source à l’aide de **Extractors.Tsv()**, puis crée un fichier csv à l’aide de **Outputters.Csv()**. 
    
    Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  Données Lake Analytique créera le dossier de sortie s’il n’existe pas.
    
    Il est plus simple d’utiliser les chemins d’accès relatifs pour les fichiers stockés par défaut données comptes Lake. Vous pouvez également utiliser les chemins d’accès absolus.  Par exemple 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe pour les fichiers stockés dans un compte de stockage Azure lié est la suivante :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Conteneur Blob Azure disposant d’autorisations d’accès conteneurs public ou des objets BLOB publics ne sont actuellement pas pris en charge.      

    
**Soumettre le travail**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Les commandes suivantes peuvent servir de la liste tâches, obtenir des détails d’une tâche et annuler les travaux :

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Une fois que la tâche est terminée, vous pouvez utiliser les applets de commande suivantes pour le fichier s’affiche et téléchargez le fichier :
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Voir aussi

- Pour afficher le même didacticiel à l’aide d’autres outils, cliquez sur les sélecteurs onglet en haut de la page.
- Pour plus d’une requête plus complexe, voir [journaux d’analyse Web à l’aide de Azure données Lake Analytique](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, voir [scripts U-SQL développer à l’aide de données Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour plus d’U-SQL, voir [prise en main langue données Lake Analytique U-SQL Azure](data-lake-analytics-u-sql-get-started.md).
- Pour les tâches de gestion, voir [Gérer des Analytique des Lake Azure données à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de données Lake Analytique, voir [vue d’ensemble Azure données Lake Analytique](data-lake-analytics-overview.md).

