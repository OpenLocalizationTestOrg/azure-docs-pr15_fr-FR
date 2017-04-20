<properties
    pageTitle="Télécharger des données pour les projets Hadoop de HDInsight | Microsoft Azure"
    description="Découvrez comment télécharger et accéder aux données pour les projets Hadoop de HDInsight à l’aide de l’infrastructure du langage commun Azure, Explorateur de stockage Azure, Azure PowerShell, la ligne de commande Hadoop ou Sqoop."
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>



#<a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Télécharger des données pour les projets Hadoop de HDInsight

Azure HDInsight fournit un système complètes de fichier Hadoop distribué (HDFS) par rapport au stockage Blob Azure. Il est conçu comme une extension HADOOP pour fournir une expérience transparente aux clients. Il permet à l’ensemble des composants dans le réseau de Hadoop pour fonctionner directement sur les données qu’il gère. Stockage d’objets Blob Azure et de HADOOP est des systèmes de fichier distinct qui sont optimisés pour le stockage des données et des calculs sur ces données. Pour plus d’informations sur les avantages de l’utilisation de stockage d’objets Blob Azure, voir [utiliser Azure Blob storage avec HDInsight][hdinsight-storage].

**Conditions préalables**

Notez les conditions suivantes avant de commencer :

* Un cluster Azure HDInsight. Pour plus d’informations, voir [prise en main Azure HDInsight] [ hdinsight-get-started] ou [groupes de mise en service HDInsight][hdinsight-provision].

##<a name="why-blob-storage"></a>Stockage blob pourquoi ?

Azure HDInsight clusters sont généralement déployées pour exécuter des travaux MapReduce, et les groupes sont supprimées après que ces tâches terminent. Tout en conservant les données dans le HADOOP clusters une fois terminées calculs serait un moyen coûteux de stocker ces données. Stockage d’objets Blob Azure est une option de stockage économique et pouvant être partagé pour les données soient traités à l’aide de HDInsight hautement disponible, scalable très haute capacité. Stocker des données dans un blob permet les groupes HDInsight qui sont utilisés pour le calcul d’être publiées en toute sécurité sans perte de données.

###<a name="directories"></a>Répertoires

Les conteneurs de stockage Blob Azure stockent les données sous forme de paires clé/valeur, et il n’existe aucune hiérarchie de répertoire. Cependant le caractère « / » peut être utilisé dans le nom de la clé pour le faire apparaître comme si un fichier est stocké dans une structure de répertoire. HDInsight voit ces comme s’ils sont des répertoires réels.

Par exemple, clé d’un blob est peut-être *input/log1.txt*. Aucun répertoire « entrée » réel n’existe, mais en raison de la présence du caractère « / » dans le nom de clé, il a l’apparence d’un chemin d’accès.

Pour cette raison, si vous utilisez les outils de l’Explorateur Azure, vous pouvez remarquer certains fichiers de 0 octet. Ces fichiers sont utilisés à des fins de deux :

- S’il existe des dossiers vides, ils marquer l’existence du dossier. Stockage d’objets Blob Azure est assez intelligent pour savoir si un blob appelé foo/barre existe, il existe un dossier nommé **foo**. Mais le seul moyen pour indiquer un dossier vide appelé **foo** est en demandant ce fichier spécial 0 octet en place.

- Ils contiennent des métadonnées spéciale qui sont requis par le système de fichier Hadoop, notamment les autorisations et les propriétaires pour les dossiers.

##<a name="command-line-utilities"></a>Utilitaires de ligne de commande

Microsoft fournit les utilitaires suivants pour l’utiliser avec stockage d’objets Blob Azure :

| Outil | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interface de ligne de commande Azure][azurecli] | ✔ | ✔ | ✔ |
| [PowerShell Azure][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Commande Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Tandis que l’infrastructure du langage commun Azure, Azure PowerShell et AzCopy peuvent toutes être utilisé à partir d’Azure extérieur, la Hadoop commande n’est disponible que sur le cluster HDInsight et uniquement permet de chargement des données à partir du système de fichiers local dans stockage d’objets Blob Azure.

###<a id="xplatcli"></a>Azure infrastructure du langage commun

L’infrastructure du langage commun Azure est un outil de disponibilité sur plusieurs plateformes qui vous permet de gérer les services Azure. Procédez comme suit pour télécharger des données sur stockage d’objets Blob Azure :

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Installer et configurer l’infrastructure du langage commun Azure pour Mac, Linux et Windows](../xplat-cli-install.md).

2. Ouvrez une invite de commande, bash ou autres shell et utilisez ce qui suit s’authentifier à votre abonnement Azure.

        azure login

    Lorsque vous y êtes invité, entrez le nom d’utilisateur et mot de passe pour votre abonnement.

3. Entrez la commande suivante pour répertorier les comptes de stockage pour votre abonnement :

        azure storage account list

4. Sélectionnez le compte de stockage qui contient le blob que vous souhaitez utiliser, puis utilisez la commande suivante pour récupérer la clé pour ce compte :

        azure storage account keys list <storage-account-name>

    Cela doit renvoyer des clés **principales** et **secondaires** . Copiez la valeur de clé **primaire** , car il sera utilisé dans les étapes suivantes.

5. Utilisez la commande suivante pour récupérer une liste de conteneurs blob à l’intérieur du compte de stockage :

        azure storage container list -a <storage-account-name> -k <primary-key>

6. Utiliser les commandes suivantes pour charger et télécharger des fichiers au blob :

    * Pour télécharger un fichier :

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * Pour télécharger un fichier :

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Si vous travaillez toujours avec le même compte de stockage, vous pouvez définir les variables d’environnement au lieu de spécifier le compte et clé pour chaque commande :
>
> * **AZURE\_stockage\_compte**: le nom de compte de stockage
>
> * **AZURE\_stockage\_ACCESS\_clé**: la clé de compte de stockage

###<a id="powershell"></a>PowerShell Azure

Azure PowerShell est un environnement de script que vous pouvez utiliser pour contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d’informations sur la configuration de votre poste de travail pour exécuter Azure PowerShell, consultez [installer et configurer Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Pour télécharger un fichier local sur stockage d’objets Blob Azure**

1. Ouvrez la console PowerShell Azure en suivant les instructions dans [installer et configurer Azure PowerShell](../powershell-install-configure.md).
2. Définissez les valeurs des cinq premiers variables dans le script suivant :

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Collez le script dans la console Azure PowerShell pour l’exécuter pour copier le fichier.

Par exemple des scripts PowerShell conçus pour fonctionner avec HDInsight, voir [Outils HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy est un outil de ligne de commande qui est conçu pour simplifier la tâche de transfert de données vers et depuis un compte de stockage Azure. Vous pouvez l’utiliser comme un outil autonome ou incorporer cet outil dans une application existante. [Télécharger AzCopy][azure-azcopy-download].

La syntaxe de la AzCopy est la suivante :

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Pour plus d’informations, voir [AzCopy - chargement/téléchargement de fichiers pour les objets BLOB Azure][azure-azcopy].


###<a id="commandline"></a>Ligne de commande Hadoop

La ligne de commande Hadoop n’est utile que pour le stockage de données dans le stockage blob lorsque les données sont déjà présentes sur le nœud de tête cluster.

Pour pouvoir utiliser la commande Hadoop, vous devez d’abord vous connecter à la headnode à l’aide d’une des méthodes suivantes :

* **HDInsight fonctionnant sous Windows**: [se connecter à l’aide de bureau à distance](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight basé sur Linux**: se connecter à l’aide de SSH ([la commande SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) ou [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Une fois connecté, vous pouvez utiliser la syntaxe suivante pour télécharger un fichier sur le stockage.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Par exemple,`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Étant donné que le système de fichiers par défaut pour HDInsight est dans le stockage Blob Azure, /example/data.txt est en réalité dans le stockage Blob Azure. Vous pouvez également consulter le fichier en tant que :

    wasbs:///example/data/data.txt

ou

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Pour une liste des autres commandes Hadoop qui fonctionnent avec des fichiers, voir [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [AZURE.WARNING] Sur les clusters HBase, la valeur par défaut bloquer taille utilisée lors de l’écriture de données est 256 Ko. Alors que cela fonctionne bien lorsque vous utilisez HBase APIs ou API REST, à l’aide du `hadoop` ou `hdfs dfs` commandes d’écrire des données plues ~ 12 Go génère une erreur. Consultez la section [exception de stockage pour écrire sur blob](#storageexception) ci-dessous pour plus d’informations.

##<a name="graphical-clients"></a>Clients graphiques

Il existe également plusieurs applications qui fournissent une interface graphique pour l’utilisation de stockage Azure. Voici une liste de certains de ces applications :

| Client | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Microsoft Visual Studio Tools pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Explorateur de stockage Azure](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Studio de stockage cloud 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Explorer Azure](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a name="visual-studio-tools-for-hdinsight"></a>Outils Visual Studio pour HDInsight

Pour plus d’informations, voir [accéder les ressources liées](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

###<a id="storageexplorer"></a>Explorateur de stockage Azure

*Explorateur de stockage Azure* est un outil utile pour inspecter et modifier les données dans des objets BLOB. Il est un outil gratuit, open source qui peut être téléchargé à partir de [http://storageexplorer.com/](http://storageexplorer.com/). Le code source est disponible sur ce lien également.

Avant d’utiliser l’outil, vous devez connaître votre clé de compte et nom de compte stockage Azure. Pour obtenir des instructions sur l’obtention de ces informations, voir la « Comment : vue, de copie et de stockage régénérer les touches d’accès « section de [créer, gérer, ou supprimer un compte de stockage][azure-create-storage-account].  

1. Exécuter l’Explorateur de stockage Azure. Si c’est la première fois que vous avez exécuté l’Explorateur d’espace de stockage, vous serez invité pour les ___nom de compte de stockage__ et de __clé de compte de stockage__. Si vous avez exécuté avant, utilisez le bouton __Ajouter__ pour ajouter un nouveau nom de compte de stockage et une clé.

    Entrez le nom et la clé pour le compte de stockage utilisé par votre cluster HDinsight, puis sélectionnez __Enregistrer et ouvrir__.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]

5. Dans la liste des conteneurs à gauche de l’interface, cliquez sur le nom du conteneur associé à votre cluster HDInsight. Par défaut, il est le nom du cluster HDInsight, mais peut être différent si vous avez entré un nom spécifique lors de la création du cluster.

6. Dans la barre d’outils, sélectionnez l’icône de téléchargement.

    ![Barre d’outils avec l’icône de téléchargement en surbrillance](./media/hdinsight-upload-data/toolbar.png)

7. Spécifier un fichier à télécharger, puis cliquez sur **Ouvrir**. Lorsque vous y êtes invité, sélectionnez __Télécharger__ pour télécharger le fichier à la racine du conteneur de stockage. Si vous souhaitez télécharger le fichier dans un chemin d’accès spécifique, entrez le chemin d’accès dans le champ de __Destination__ , puis sélectionnez __Télécharger__.

    ![Boîte de dialogue fichier téléchargement](./media/hdinsight-upload-data/fileupload.png)
    
    Une fois que le fichier de téléchargement est terminé, vous pouvez l’utiliser à partir de tâches sur le cluster HDInsight.

##<a name="mount-azure-blob-storage-as-local-drive"></a>Stockage d’objets Blob Azure montage sous la forme d’un lecteur Local

Voir [stockage d’objets Blob Azure montage en tant que lecteur Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##<a name="services"></a>Services

###<a name="azure-data-factory"></a>Données Azure usine

Le service Azure Data Factory est un service entièrement géré pour composer des services de déplacement de données stockage, le traitement des données et des données dans des pipelines de production rationalisée, scalable et fiable des données.

Azure Data Factory peut servir pour déplacer des données dans le stockage Blob Azure, ou pour créer les pipelines de données qui utilisent directement HDInsight fonctionnalités telles que Hive et cochon.

Pour plus d’informations, voir la [documentation Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop est un outil conçu pour transférer des données entre Hadoop et bases de données relationnelles. Vous pouvez l’utiliser pour importer des données à partir d’un système de gestion de base de données relationnelle (SGBDR), telles que SQL Server, MySQL ou Oracle dans le système de fichiers Hadoop distributed (HDFS), transformer les données dans Hadoop avec MapReduce ou Hive et puis exporter les données dans un SGBDR.

Pour plus d’informations, voir [Utiliser Sqoop avec HDInsight][hdinsight-use-sqoop].

##<a name="development-sdks"></a>Développement SDK

Stockage d’objets Blob Azure également accessibles à l’aide d’un kit de développement Azure à partir des programmation langues suivantes :

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Pour plus d’informations sur l’installation du SDK Azure, voir [téléchargements d’Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Résolution des problèmes

### <a id="storageexception"></a>Exception de stockage pour écrire sur blob

__Symptômes__: lorsque vous utilisez la `hadoop` ou `hdfs dfs` commandes pour écrire des fichiers qui sont ~ 12 Go ou plus sur un cluster HBase, vous pouvez rencontrer l’erreur suivante : 

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

__Cause__: HBase sur HDInsight clusters par défaut pour une taille de bloc de 256 Ko lors de l’écriture au stockage Azure. Alors que cela fonctionne pour HBase APIs ou API REST, elle provoquera une erreur lorsque vous utilisez la `hadoop` ou `hdfs dfs` utilitaires de ligne de commande.

__Résolution__: utilisation `fs.azure.write.request.size` pour spécifier une plus grande taille de bloc. Vous pouvez le faire sur une base de coûts d’utilisation à l’aide de la `-D` paramètre. Voici un exemple d’utilisation de ce paramètre avec le `hadoop` commande :

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

Vous pouvez également augmenter la valeur de `fs.azure.write.request.size` globalement à l’aide de Ambari. Les étapes suivantes peuvent être utilisés pour modifier la valeur dans l’interface utilisateur Web Ambari :

1. Dans votre navigateur, accédez à l’interface utilisateur Web Ambari pour votre cluster. Il s’agit https://CLUSTERNAME.azurehdinsight.net, où __CLUSTERNAME__ est le nom de votre cluster.

    Lorsque vous y êtes invité, entrez le nom et le mot de passe pour le cluster.

2. À partir du bord gauche de l’écran, sélectionnez __HADOOP__, puis l’onglet __configurations__ .

3. Dans le champ __filtrer...__ , entrez `fs.azure.write.request.size`. Le champ et la valeur actuelle au milieu de la page s’affiche.

4. Remplacez la valeur 262144 (256 Ko) à la nouvelle valeur. Par exemple, 4194304 (4 Mo).

![Image illustrant le changement de la valeur via l’interface utilisateur de Ambari Web](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Pour plus d’informations sur l’utilisation de Ambari, voir [clusters HDInsight gérer à l’aide de l’interface utilisateur Web Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment obtenir les données dans HDInsight, lisez les articles suivants pour savoir comment effectuer une analyse :

* [Prise en main Azure HDInsight][hdinsight-get-started]
* [Soumettre des travaux d’Hadoop par programme][hdinsight-submit-jobs]
* [Utiliser Hive avec HDInsight][hdinsight-use-hive]
* [Utiliser cochon avec HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
