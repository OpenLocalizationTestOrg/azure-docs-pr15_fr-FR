<properties
   pageTitle="Copiez les données des objets BLOB Azure stockage dans données Lake magasin | Microsoft Azure"
   description="Utilisez l’outil AdlCopy pour copier les données d’objets BLOB Azure stockage au magasin Lake des données"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copier des données d’objets BLOB Azure stockage au magasin Lake des données

> [AZURE.SELECTOR]
- [À l’aide de DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [À l’aide de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Magasin de Lake données Azure fournit un outil de ligne de commande, [AdlCopy](http://aka.ms/downloadadlcopy), pour copier les données dans les sources suivantes :

* À partir des objets BLOB Azure stockage dans données Lake magasin. Vous ne pouvez pas utiliser AdlCopy pour copier les données à partir de données Lake Store dans des objets BLOB Azure stockage.

* Entre deux comptes Azure données Lake Store. 

En outre, vous pouvez utiliser l’outil AdlCopy en deux modes différents :

* **Autonome**, en l’endroit où l’outil utilise les ressources de données Lake Store pour effectuer la tâche.

* **À l’aide d’un compte de données Lake Analytique**, où les unités affectées à votre compte de données Lake Analytique sont utilisées pour effectuer l’opération de copie. Vous souhaiterez peut-être utiliser cette option lorsque vous recherchez pour effectuer les tâches de copie d’une manière prévisible.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- Conteneur **d’Objets BLOB Azure stockage** avec des données.

- **Compte azure données Lake Analytique (facultatif)** - en voir [prise en main Azure données Lake Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pour obtenir des instructions sur la création d’un compte de données Lake Store.

- **Outil AdlCopy**. Installez l’outil AdlCopy à partir de [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxe de l’outil AdlCopy

Utilisez la syntaxe suivante pour fonctionner avec l’outil AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Les paramètres dans la syntaxe sont décrits ci-après :

| Option    | Description                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| Source    | Spécifie l’emplacement de la source de données dans le blob Azure stockage. La source peut être un conteneur blob, un blob ou un autre compte de données Lake Store.                                                                                                                                                                                                                                                                                                 |
| Dest      | Spécifie la destination de données Lake Store pour copier vers.                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | Spécifie la clé d’accès de stockage pour la source d’objets blob Azure stockage. Ceci est requis uniquement si la source est un conteneur blob ou un blob.                                                                                                                                                                                                                                                                                                                                                 |
| Compte   | **Facultatif**. Utilisez cette option si vous voulez utiliser compte Azure données Lake Analytique pour exécuter la tâche de copie. Si vous utilisez l’option/Account dans la syntaxe de la mais que vous ne spécifiez pas un compte de données Lake Analytique, AdlCopy utilise un compte par défaut pour exécuter la tâche. En outre, si vous utilisez cette option, vous devez ajouter source (Azure stockage Blob) et destination (Azure données Lake Store) en tant que sources de données pour votre compte de données Lake Analytique.  |
| Unités     |  Spécifie le nombre d’unités de données Lake Analytique qui sera utilisé pour le travail de copie. Cette option est obligatoire si vous utilisez **l’option/Account** pour spécifier le compte de données Lake Analytique.
| Motif   | Spécifie un modèle regex qui indique les objets BLOB ou les fichiers à copier. AdlCopy utilise correspondant à la casse. Le modèle par défaut utilisé lorsqu’aucun modèle n’est spécifié consiste à copier tous les éléments. Spécification plusieurs modèles de fichier n’est pas prise en charge.                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utiliser AdlCopy (comme autonome) pour copier les données à partir d’un blob Azure Storage

1. Ouvrez une invite de commandes et accédez au répertoire où AdlCopy est installé, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un blob spécifique du conteneur source dans un magasin Lake données :

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Par exemple :

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] La syntaxe ci-dessus indique le fichier à copier vers un dossier dans le compte données Lake. Outil AdlCopy crée un dossier si le nom du dossier spécifié n’existe pas.

    Vous devrez entrer les informations d’identification pour l’abonnement Azure sous lequel vous avez votre compte données Lake Store. Vous verrez un résultat semblable à ce qui suit :

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. Vous pouvez également copier tous les objets BLOB à partir d’un seul conteneur au compte données Lake Store à l’aide de la commande suivante :

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Par exemple :

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Utiliser AdlCopy (comme autonome) pour copier les données à partir d’un autre compte de données Lake Store

Vous pouvez également utiliser AdlCopy pour copier des données entre deux comptes données Lake Store.

1. Ouvrez une invite de commandes et accédez au répertoire où AdlCopy est installé, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un fichier spécifique d’un compte de données Lake Store à l’autre.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Par exemple :

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] La syntaxe ci-dessus indique le fichier à copier vers un dossier dans la destination du compte données Lake Store. Outil AdlCopy crée un dossier si le nom du dossier spécifié n’existe pas.

    Vous devrez entrer les informations d’identification pour l’abonnement Azure sous lequel vous avez votre compte données Lake Store. Vous verrez un résultat semblable à ce qui suit :

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. La commande suivante copie tous les fichiers à partir d’un dossier spécifique dans la source de données Lake compte à un dossier dans la destination du compte données Lake Store.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilisez AdlCopy (avec compte données Lake Analytique) pour copier des données

Vous pouvez également utiliser votre compte de données Lake Analytique pour exécuter la tâche AdlCopy pour copier les données d’objets BLOB Azure stockage au magasin Lake des données. Vous utiliserez généralement cette option lorsque les données à déplacer figurent dans la plage de gigaoctets et To et souhaitez débit performances améliorées et prévisibles.

Pour utiliser votre compte de données Lake Analytique avec AdlCopy pour copier un Blob de stockage Azure, la source (Azure stockage Blob) doit être ajoutée comme source de données pour votre compte de données Lake Analytique. Pour obtenir des instructions sur l’ajout de sources de données supplémentaires à votre compte de données Lake Analytique, voir [Gérer les données Lake Analytique du compte des sources de données](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Si vous effectuez la copie d’un compte Azure données Lake Store comme source à l’aide d’un compte de données Lake Analytique, vous n’avez pas besoin associer le compte données Lake Store avec le compte de données Lake Analytique. La configuration minimale requise pour associer le magasin source au compte données Lake Analytique est uniquement si la source est un compte de stockage Azure.

Exécutez la commande suivante pour copier un blob Azure Storage à un compte de données Lake Store à l’aide des données Lake Analytique compte :

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Par exemple :

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


De même, exécutez la commande suivante pour copier un blob Azure Storage à un compte de données Lake Store à l’aide des données Lake Analytique compte :

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Permet de copier des données à l’aide de correspondances AdlCopy

Dans cette section, vous découvrez comment utiliser AdlCopy pour copier les données d’une source (dans notre exemple ci-dessous utilisons-nous Azure stockage Blob) à un compte de magasin Lake de données de destination à l’aide de correspondance. Par exemple, vous pouvez utiliser la procédure ci-dessous pour copier tous les fichiers avec l’extension de fichier .csv à partir du blob source à la destination.

1. Ouvrez une invite de commandes et accédez au répertoire où AdlCopy est installé, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier tous les fichiers avec l’extension *.csv à partir d’un blob spécifique du conteneur source dans un magasin Lake de données :

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Par exemple :

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>Facturation

* Si vous utilisez l’outil AdlCopy instance autonome facturation des coûts de sortie de déplacement des données, si la source de compte de stockage Azure n’est pas dans la même région comme magasin Lake de données.

* Si vous utilisez l’outil AdlCopy avec votre compte de données Lake Analytique, standard de [taux de facturation de données Lake Analytique](https://azure.microsoft.com/pricing/details/data-lake-analytics/) s’appliquent.

## <a name="considerations-for-using-adlcopy"></a>Considérations sur l’utilisation de AdlCopy

* AdlCopy (pour la version 1.0.5), prend en charge la copie de données à partir de sources collectivement ont plus de milliers de fichiers et dossiers. Toutefois, si vous rencontrez des problèmes copie d’un jeu de données volumineux, vous pouvez distribuer les fichiers/dossiers dans différents sous-dossiers et utiliser le chemin d’accès à ces sous-dossiers comme source à la place.

## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
