<properties
    pageTitle="Copier ou déplacer des données vers le stockage avec AzCopy | Microsoft Azure"
    description="Utiliser l’utilitaire AzCopy pour déplacer ou copier des données vers ou à partir d’objets blob, table et contenu d’un fichier. Copiez les données au stockage Azure des fichiers locaux ou copier des données dans ou entre des comptes de stockage. Migrer facilement vos données vers le stockage Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>Transférer des données avec l’utilitaire de ligne de commande AzCopy

## <a name="overview"></a>Vue d’ensemble

AzCopy est un utilitaire de ligne de commande Windows conçu pour copier les données vers et depuis Microsoft Azure Blob, fichier et Table storage à l’aide de commandes simples avec des performances optimales. Vous pouvez copier les données d’un objet à l’autre au sein de votre compte de stockage ou entre des comptes de stockage.

> [AZURE.NOTE] Ce guide suppose que vous êtes déjà familiarisé avec le [Stockage Azure](https://azure.microsoft.com/services/storage/). Dans le cas contraire, lisez la documentation [d’Introduction au stockage Azure](storage-introduction.md) vous seront utile. Surtout, vous aurez besoin pour [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) pour commencer à utiliser AzCopy.

## <a name="download-and-install-azcopy"></a>Téléchargez et installez AzCopy

### <a name="windows"></a>Windows

Téléchargez la [dernière version d’AzCopy](http://aka.ms/downloadazcopy).

### <a name="maclinux"></a>Mac/Linux

AzCopy n’est pas disponible pour les systèmes d’exploitation Mac/Linux. Toutefois, infrastructure du langage commun Azure constitue une solution pour copier les données vers et depuis le stockage Azure. Lire [à l’aide de l’infrastructure du langage commun Azure avec stockage Azure](storage-azure-cli.md) pour en savoir plus.

## <a name="writing-your-first-azcopy-command"></a>Rédaction de votre première commande AzCopy

La syntaxe de la base de commandes AzCopy est la suivante :

    AzCopy /Source:<source> /Dest:<destination> [Options]

Ouvrez une fenêtre de commande et accédez au répertoire d’installation AzCopy sur votre ordinateur - où la `AzCopy.exe` exécutable se trouve. Si vous le souhaitez, vous pouvez ajouter l’emplacement d’installation AzCopy chemin sur votre système. Par défaut, AzCopy est installé dans `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

Les exemples suivants illustrent une gamme des scénarios pour la copie de données et d’objets BLOB Microsoft Azure, les fichiers et les Tables. Reportez-vous à la section [Paramètres AzCopy](#azcopy-parameters) pour une explication détaillée des paramètres utilisés dans chaque échantillon.

## <a name="blob-download"></a>Objets BLOB : télécharger

### <a name="download-single-blob"></a>Télécharger blob unique

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Notez que si le dossier `C:\myfolder` n’existe pas, AzCopy crée et télécharger `abc.txt ` dans le nouveau dossier.

### <a name="download-single-blob-from-secondary-region"></a>Télécharger blob unique à partir de la zone secondaire

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Notez que vous devez avoir accès en lecture stockage geo redondants activé.

### <a name="download-all-blobs"></a>Télécharger tous les objets BLOB

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Supposons que les objets BLOB suivants résident dans le conteneur spécifié :  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Une fois l’opération de téléchargement, le répertoire `C:\myfolder` doit inclure les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Si vous ne spécifiez pas l’option `/S`, aucun des objets BLOB ne sont téléchargés.

### <a name="download-blobs-with-specified-prefix"></a>Télécharger des objets BLOB avec préfixe spécifié

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Supposons que les objets BLOB suivants résident dans le conteneur spécifié. Tous les objets BLOB commençant par le préfixe `a` sont téléchargés :

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Une fois l’opération de téléchargement, le dossier `C:\myfolder` doit inclure les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Le préfixe s’applique au répertoire virtuel, lequel constitue la première partie du nom du blob. Dans l’exemple ci-dessus, le répertoire virtuel ne correspond pas le préfixe spécifié, afin qu’il n’est pas téléchargée. En outre, si l’option `\S` n’est pas spécifié, AzCopy ne télécharge pas les objets BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Définir l’heure de dernière modification de fichiers exportés à être identique à celui les objets BLOB source

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

Vous pouvez également exclure des objets BLOB de l’opération de téléchargement en fonction de leur date de dernière modification. Par exemple, si vous voulez exclure des objets BLOB heure de dernière modification est la même ou plus récent que le fichier de destination, ajoutez la `/XN` option :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

Ou si vous voulez exclure des objets BLOB heure de dernière modification est la même ou plus anciens que le fichier de destination, ajoutez la `/XO` option :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>Objets BLOB : télécharger

### <a name="upload-single-file"></a>Télécharger le fichier unique

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Si le conteneur de destination spécifié n’existe pas, AzCopy il crée et téléchargez le fichier dedans.

### <a name="upload-single-file-to-virtual-directory"></a>Télécharger le fichier unique au répertoire virtuel

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Si le répertoire virtuel spécifié n’existe pas, AzCopy pour télécharger le fichier pour inclure le répertoire virtuel dans son nom (*par exemple*, `vd/abc.txt` dans l’exemple ci-dessus).

### <a name="upload-all-files"></a>Télécharger tous les fichiers

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

L’option `/S` télécharge le contenu du répertoire spécifié au récursive stockage Blob, ce qui signifie que tous les sous-dossiers et ses fichiers seront téléchargés également. Par exemple, supposons que les fichiers suivants se trouvent dans le dossier `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Une fois l’opération de téléchargement, le conteneur doit inclure les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy télécharge pas de manière récursive. Une fois l’opération de téléchargement, le conteneur doit inclure les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>Télécharger des fichiers correspondant au modèle spécifié

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Supposons que les fichiers suivants se trouvent dans le dossier `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Une fois l’opération de téléchargement, le conteneur doit inclure les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy sont-elles uniquement des objets BLOB qui ne se trouvent dans un répertoire virtuel :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Spécifier le type de contenu MIME d’un objet blob de destination

Par défaut, AzCopy définit le type de contenu d’un objet blob destination à `application/octet-stream`. Depuis la version 3.1.0, vous pouvez spécifier explicitement le type de contenu via l’option `/SetContentType:[content-type]`. La syntaxe suivante définit le type de contenu pour tous les objets BLOB dans une opération de téléchargement.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Si vous spécifiez `/SetContentType` sans une valeur, puis AzCopy définira chaque blob ou type de contenu du fichier en fonction de son extension de fichier.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>Objets BLOB : copie

### <a name="copy-single-blob-within-storage-account"></a>Copier des objets blob unique au sein de compte de stockage

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Lorsque vous copiez un blob au sein d’un compte de stockage, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est effectuée.

### <a name="copy-single-blob-across-storage-accounts"></a>Copier des blob unique dans des comptes de stockage

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Lorsque vous copiez un blob entre des comptes de stockage, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est effectuée.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copiez blob unique région secondaire à une zone principale

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Notez que vous devez avoir accès en lecture stockage geo redondants activé.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copier blob unique et ses instantanés entre des comptes de stockage

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Après l’opération de copie, le conteneur cible doit inclure le blob et ses instantanés. En supposant que le blob dans l’exemple ci-dessus comporte deux instantanés, le conteneur doit inclure les objets blob et des instantanés suivants :

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Synchrone copier des objets BLOB dans des comptes de stockage

AzCopy par défaut copie asynchrone des données entre deux points de terminaison de stockage. Par conséquent, l’opération de copie est exécuté en arrière-plan en utilisant la bande passante capacité aucun SLA en termes de façon rapide un blob est copié et AzCopy doit vérifier régulièrement le statut de copie jusqu'à ce que la copie est terminée ou a échoué.

La `/SyncCopy` option garantit que l’opération de copie n’obtiendrez vitesse cohérente. AzCopy effectue la copie synchrone en téléchargeant les objets BLOB pour copier de la source spécifiée vers la mémoire locale, puis les télécharger vers la destination de stockage Blob.

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`peut générer un coût de sortie supplémentaires par rapport à la copie asynchrone, l’approche recommandée consiste à utiliser cette option dans une machine virtuelle Azure qui se trouve dans la même région en tant que votre compte de stockage source pour éviter les coûts de sortie.

## <a name="file-download"></a>Fichier : télécharger

### <a name="download-single-file"></a>Télécharger le fichier unique

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si la source spécifiée est un partage de fichiers Azure, puis vous devez spécifier le nom de fichier exact (*par exemple,* `abc.txt`) pour télécharger un fichier unique, ou spécifier option `/S` pour télécharger tous les fichiers dans le partager de manière récursive. Tentative de spécifier un modèle de fichier et l’option `/S` ensemble provoquera une erreur.

### <a name="download-all-files"></a>Télécharger tous les fichiers

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Notez que les dossiers vides ne sont pas téléchargés.

## <a name="file-upload"></a>Fichier : télécharger

### <a name="upload-single-file"></a>Télécharger le fichier unique

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>Télécharger tous les fichiers

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Notez que les dossiers vides ne sont pas téléchargées.

### <a name="upload-files-matching-specified-pattern"></a>Télécharger des fichiers correspondant au modèle spécifié

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>Fichier : copie

### <a name="copy-across-file-shares"></a>Copier dans des partages de fichiers

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>Copier partage de fichiers en blob

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Notez que copie asynchrone de stockage de fichiers vers Blob de Page n’est pas pris en charge.

### <a name="copy-from-blob-to-file-share"></a>Copiez blob au partage de fichiers

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>Copier des fichiers simultanément

Vous pouvez spécifier la `/SyncCopy` une autre option pour copier des données à partir du stockage de fichier pour le stockage de fichiers, à partir de stockage de fichiers de stockage d’objets Blob et de stockage d’objets Blob pour le stockage de fichiers simultanément, AzCopy pour cela, le téléchargement de la source de données vers la mémoire locale et télécharger à nouveau vers la destination.

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Lorsque vous copiez à partir de stockage de fichiers vers le stockage Blob, le type d’objets blob par défaut est blob bloc, utilisateur peut spécifier l’option `/BlobType:page` pour modifier le type de blob de destination.

Notez que `/SyncCopy` peut générer sortie supplémentaire coût comparaison à copie asynchrone, l’approche recommandée consiste à utiliser cette option dans la machine virtuelle Azure qui se trouve dans la même région en tant que votre compte de stockage source pour éviter les coûts de sortie.

## <a name="table-export"></a>Tableau : exporter

### <a name="export-table"></a>Exporter le tableau

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy écrit un fichier manifeste dans le dossier de destination spécifié. Le fichier manifest est utilisé dans le processus d’importation pour localiser les fichiers de données nécessaires et effectuer la validation des données. Le fichier manifeste utilise la dénomination suivante par défaut :

    <account name>_<table name>_<timestamp>.manifest

Utilisateur peut également spécifier l’option `/Manifest:<manifest file name>` pour définir le nom de fichier manifeste.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>Exportation fractionnée en plusieurs fichiers

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utilise un *indice de volume* dans les noms de fichiers de données fractionnée pour distinguer plusieurs fichiers. L’index de volume se compose de deux parties, un *index de plage de clés partition* et *Fractionner le fichier d’index*. Les deux index commencent par zéro.

L’index de la plage de clés partition est égal à 0 si l’utilisateur n’en spécifie option `/PKRS`.

Par exemple, supposons que AzCopy génère deux fichiers de données une fois que l’utilisateur spécifie l’option `/SplitSize`. Les noms de fichier de données qui en résulte peuvent être :

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Remarque Cette valeur minimale possibles pour l’option `/SplitSize` est de 32 Mo. Si la destination spécifiée est stockage Blob, AzCopy sera fractionner le fichier de données une fois que sa taille atteint la limite de taille de blob (200 Go), indépendamment de si l’option `/SplitSize` a été spécifié par l’utilisateur.

### <a name="export-table-to-json-or-csv-data-file-format"></a>Exporter le tableau au format de fichier de données JSON ou CSV

AzCopy par défaut exporte les tables de fichiers de données JSON. Vous pouvez spécifier l’option `/PayloadFormat:JSON|CSV` pour exporter les tables en tant que JSON ou CSV.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Lorsque vous spécifiez le format de charge utile CSV, AzCopy génère également un fichier de schéma avec l’extension de fichier `.schema.csv` pour chaque fichier de données.

### <a name="export-table-entities-concurrently"></a>Exporter tableau entités simultanément

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy démarre opérations simultanées pour exporter entités lorsque l’utilisateur spécifie l’option `/PKRS`. Chaque opération exporte une plage de clés partition.

Notez que le nombre d’opérations simultanées est également contrôlé par option `/NC`. AzCopy utilise le nombre de processeurs comme valeur par défaut de `/NC` lors de la copie entités de table, même si `/NC` n’a été spécifié. Lorsque l’utilisateur spécifie option `/PKRS`, AzCopy utilise la plus petite des deux valeurs : plages de clés partition et implicitement ou explicitement spécifié opérations simultanées - à déterminer le nombre d’opérations simultanées pour commencer. Pour plus d’informations, tapez `AzCopy /?:NC` à la ligne de commande.

### <a name="export-table-to-blob"></a>Exporter le tableau à blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy génère un fichier de données JSON dans le conteneur blob avec suivant convention d’appellation :

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Fichier de données JSON généré suit le format de charge utile pour les métadonnées minimales. Pour plus d’informations sur ce format de charge utile, voir [Format de charge utile pour les opérations de Service de Table](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Notez que lorsque vous exportez des tables à des objets BLOB, AzCopy télécharger les entités de Table pour les fichiers de données temporaires locaux et puis téléchargez ces entités dans le blob. Ces fichiers de données temporaires sont placés dans le dossier du fichier journal avec le chemin d’accès par défaut «<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>», vous pouvez spécifier option/z : [dossier de fichier journal] pour modifier la feuille emplacement du dossier de fichiers et modifier ainsi l’emplacement des fichiers temporaires de données. Taille des fichiers de données temporaires est déterminée par la taille de votre entités tableau et la taille que vous avez spécifié avec l’option /SplitSize, bien que le fichier de données temporaire de disque local sera supprimé instantanément une fois qu’il a été téléchargé dans le blob, vérifiez que vous disposez de suffisamment d’espace disque local pour stocker ces fichiers de données temporaires avant d’être supprimées.

## <a name="table-import"></a>Tableau : importation

### <a name="import-table"></a>Table d’importation

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

L’option `/EntityOperation` indique comment insérer des entités dans la table. Les valeurs possibles sont :

- `InsertOrSkip`: Permet d’ignorer une entité existante ou insère une nouvelle entité s’il n’existe pas dans la table.
- `InsertOrMerge`: Fusionne une entité existante ou insère une nouvelle entité s’il n’existe pas dans la table.
- `InsertOrReplace`: Remplace une entité existante ou insère une nouvelle entité s’il n’existe pas dans la table.

Notez que vous ne pouvez pas spécifier option `/PKRS` dans le scénario de l’importation. Contrairement au scénario exporter, dans laquelle vous devez spécifier option `/PKRS` pour démarrer des opérations simultanées, AzCopy par défaut démarre opérations simultanées lorsque vous importez une table. Le nombre par défaut d’opérations simultanées en main est égal au nombre de processeurs ; Toutefois, vous pouvez spécifier un nombre différent de simultanées avec l’option `/NC`. Pour plus d’informations, tapez `AzCopy /?:NC` à la ligne de commande.

Notez que AzCopy prend uniquement en charge l’importation pour JSON, pas CSV. AzCopy ne pas prise en charge de la table en provenance JSON créés par l’utilisateur et fichiers manifeste. Ces deux fichiers doivent provenir d’une exportation de la table AzCopy. Pour éviter les erreurs, veuillez ne modifiez pas le JSON exporté ou fichier manifeste.

### <a name="import-entities-to-table-using-blobs"></a>Importer des entités dans la table à l’aide d’objets BLOB

Supposons un conteneur Blob contient les éléments suivants : fichier JSON A représentant une Table Azure et son fichier manifeste associé.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Vous pouvez exécuter la commande suivante pour importer des entités dans un tableau en utilisant le fichier manifeste dans ce conteneur blob :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>Autres fonctionnalités AzCopy

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copier uniquement les données qui n’existent pas dans la destination

La `/XO` et `/XN` paramètres permettent de vous permet d’exclure les ressources sources plus anciens ou version ultérieure soient copiés, respectivement. Si vous voulez uniquement copier des ressources de code source qui n’existent pas dans la destination, vous pouvez spécifier les deux paramètres dans la commande AzCopy :

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Remarque : Il n'est pas pris en charge lorsque la source ou la destination est une table.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Utiliser un fichier réponse pour spécifier les paramètres de ligne de commande

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

Vous pouvez inclure tous les paramètres de ligne de commande AzCopy dans un fichier de réponse. AzCopy traite les paramètres dans le fichier comme s’ils avaient été spécifiés dans la ligne de commande, effectuer une substitution directe avec le contenu du fichier.

Supposons un fichier de réponse nommé `copyoperation.txt`, qui contient les lignes suivantes. Chaque paramètre AzCopy peut être spécifié sur une seule ligne

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou sur des lignes distinctes :

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy échoue si vous répartir le paramètre sur deux lignes, comme illustré ici pour la `/sourcekey` paramètre :

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Utiliser plusieurs fichiers réponse pour spécifier les paramètres de ligne de commande

Supposons un fichier de réponse nommé `source.txt` qui spécifie un conteneur source :

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Et un fichier de réponse nommé `dest.txt` qui spécifie un dossier de destination dans le système de fichiers :

    /Dest:C:\myfolder

Et un fichier de réponse nommé `options.txt` indiquant les options pour AzCopy :

    /S /Y

Pour appeler AzCopy avec ces fichiers réponse, ce qui se trouvent dans un répertoire `C:\responsefiles`, utilisez cette commande :

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy traite cette commande exactement comme si vous avez inclus tous les différents paramètres sur la ligne de commande :

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>Spécifier une signature accès partagé (sa)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

Vous pouvez également spécifier une associations de sécurité sur le conteneur URI :

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>Dossier du fichier journal

Chaque fois que vous exécutez une commande à AzCopy, elle vérifie si un fichier journal existe dans le dossier par défaut, ou s’il existe dans un dossier que vous avez spécifié via cette option. Si le fichier journal n’existe pas à cet emplacement, AzCopy traite l’opération en tant que nouveau et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande dans le fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération non terminée. Si elles ne correspondent pas, vous devrez soit remplacer le fichier journal pour démarrer une nouvelle opération, ou pour annuler l’opération en cours.

Si vous voulez utiliser l’emplacement par défaut pour le fichier journal :

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Si vous omettez option `/Z`, ou spécifier l’option `/Z` sans le chemin du dossier, comme indiqué ci-dessus, AzCopy crée le fichier journal dans l’emplacement par défaut, ce qui correspond à `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si le fichier journal existe déjà, AzCopy reprend l’opération basée sur le fichier journal.

Si vous souhaitez spécifier un emplacement personnalisé pour le fichier journal :

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Cet exemple crée le fichier journal s’il n’existe pas déjà. S’il existe, AzCopy reprend l’opération basée sur le fichier journal.

Si vous souhaitez reprendre une opération AzCopy :

    AzCopy /Z:C:\journalfolder\

Cet exemple reprend la dernière opération, qui n’ont peut-être pas terminer.

### <a name="generate-a-log-file"></a>Générer un fichier journal

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Si vous spécifiez option `/V` sans fournir un chemin d’accès de fichier dans le journal des commentaires, puis AzCopy crée le fichier journal à l’emplacement par défaut, ce qui correspond à `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Dans le cas contraire, vous pouvez créer un fichier journal dans un emplacement personnalisé :

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Notez que si vous spécifiez un chemin d’accès relatif suivant option `/V`, tel que `/V:test/azcopy1.log`, puis le journal détaillé est créé dans le répertoire de travail en cours dans un sous-dossier nommé `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Spécifier le nombre d’opérations simultanées pour commencer

Option `/NC` Spécifie le nombre d’opérations de copie simultanées. Par défaut, AzCopy démarre un certain nombre d’opérations simultanées pour augmenter la vitesse de transfert de données. Pour les opérations de Table, le nombre d’opérations simultanées est égal au nombre de processeurs que vous avez. Pour opérations Blob et de fichiers, le nombre d’opérations simultanées est égal à 8 fois le nombre de processeurs que vous avez. Si vous exécutez AzCopy via un réseau de faible bande passante, vous pouvez spécifier un nombre inférieur pour /NC afin d’éviter défaillance due à la concurrence ressource.

### <a name="run-azcopy-against-azure-storage-emulator"></a>Exécutez AzCopy sur émulateur de stockage Azure

Vous pouvez exécuter AzCopy sur l' [Émulateur de stockage Azure](storage-use-emulator.md) pour les objets BLOB :

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

et des Tables :

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>Paramètres AzCopy

Paramètres de AzCopy sont décrites ci-dessous. Vous pouvez également taper une des commandes suivantes à partir de la ligne de commande pour vous aider à l’aide de AzCopy :

- Pour plus d’informations de ligne de commande pour AzCopy :`AzCopy /?`
- Pour obtenir une aide détaillée sur n’importe quel paramètre AzCopy :`AzCopy /?:SourceKey`
- Pour obtenir des exemples de ligne de commande :`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ Source : « source »

Spécifie les données source à partir duquel copier. La source peut être un répertoire de système de fichiers, un conteneur blob, un répertoire virtuel blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="destdestination"></a>/ Dest : « de destination »

Spécifie la destination pour copier vers. La destination peut être un répertoire de système de fichiers, un conteneur blob, un répertoire virtuel blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="patternfile-pattern"></a>/ Motif : « modèle de fichier »

Spécifie un modèle de fichier qui indique les fichiers à copier. Le comportement du paramètre /Pattern est déterminé par l’emplacement de la source de données et la présence de l’option mode récursive. Mode récursif est spécifié via l’option /S.

Si la source spécifiée est un répertoire dans le système de fichiers, puis les caractères génériques standards sont en vigueur et du modèle de fichier fourni est mis en correspondance avec les fichiers du répertoire. Si l’option que /s est spécifié, puis AzCopy correspond également au modèle spécifié par rapport à tous les fichiers dans des sous-dossiers en dessous de l’annuaire.

Si la source spécifiée est un conteneur blob ou le répertoire virtuel, des caractères génériques ne sont pas appliquées. Si l’option que /s est spécifié, puis AzCopy interprète le modèle de fichier spécifié comme un préfixe blob. Si l’option que /s n’est pas spécifié, puis AzCopy correspond au modèle de fichier par rapport aux noms d’objets blob exacte.

Si la source spécifiée est un partage de fichiers Azure, puis vous devez spécifier le nom de fichier exact, (par exemple, abc.txt) pour copier un seul fichier, ou spécifier l’option /S pour copier tous les fichiers dans le partager de manière récursive. Tentative de spécifier un modèle de fichier et l’option /S ensemble provoquera une erreur.

AzCopy utilise correspondant à la casse lorsque la /Source est un conteneur blob ou le répertoire virtuel blob et utilise la correspondance sans respecter la casse dans tous les autres cas.

Le modèle de fichier par défaut utilisé lorsqu’aucun modèle de fichier n’est spécifié est *.* pour un emplacement de système de fichiers ou un préfixe vide pour un emplacement de stockage Azure. Spécification plusieurs modèles de fichier n’est pas prise en charge.

**Applicables aux :** Objets BLOB, fichiers

### <a name="destkeystorage-key"></a>/ DestKey : « clé de stockage »

Spécifie la clé de compte de stockage pour la ressource de destination.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="destsassas-token"></a>/ DestSAS : « jeton de sa »

Indique une Signature de Access partagé (sa) avec des autorisations de lecture et d’écriture pour la destination (le cas échéant). Entourer les associations de sécurité entre guillemets doubles, comme il peut contient des caractères spéciaux de ligne de commande.

Si la ressource de destination est un conteneur blob, un partage de fichiers ou un tableau, vous pouvez spécifier cette option suivie du jeton de sa, ou vous pouvez spécifier les associations de sécurité dans le cadre de conteneur blob de destination, de partage de fichiers ou URI du tableau, sans cette option.

Si la source et destination sont les deux objets BLOB, le blob de destination doit résider dans le même compte de stockage que le blob source.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="sourcekeystorage-key"></a>/ SourceKey : « clé de stockage »

Spécifie la clé de compte de stockage pour la ressource de code source.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="sourcesassas-token"></a>/ SourceSAS : « jeton de sa »

Spécifie une Signature accès partagé avec des autorisations en lecture et de liste pour la source (le cas échéant). Entourer les associations de sécurité entre guillemets doubles, comme il peut contient des caractères spéciaux de ligne de commande.

Si la ressource de code source est un conteneur blob, et un sa ni une clé est fourni, le conteneur blob est lu via l’accès anonyme.

Si la source est un partage de fichiers ou une table, une clé ou une sa doit être fourni.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="s"></a>/S

Spécifie le mode récursive pour les opérations de copie. En mode récursif, AzCopy copie tous les objets BLOB ou les fichiers qui correspondent au modèle de fichier spécifié, y compris ceux présents dans des sous-dossiers.

**Applicables aux :** Objets BLOB, fichiers

### <a name="blobtypeblock--page--append"></a>/ BlobType : « bloc » | « page » | « ajouter »

Indique si le blob de destination est un blob bloc, un blob de page ou un blob Ajout. Cette option s’applique uniquement lorsque vous téléchargez un blob. Sinon, une erreur est générée. Si la destination est un blob et que cette option n’est pas spécifiée, par défaut, AzCopy crée un blob bloc.

**Applicables aux :** Objets BLOB

### <a name="checkmd5"></a>/ CheckMD5

Calcule un hachage MD5 pour les données téléchargées et vérifie que le hachage MD5 stocké dans le blob ou propriété Content-MD5 du fichier correspond au hachage calculé. La case à cocher MD5 est désactivée par défaut, vous devez spécifier cette option pour effectuer la vérification MD5 lors du téléchargement des données.

Notez que le stockage Azure ne garantit que hachage MD5 stocké pour le blob ou le fichier est à jour. Il est responsabilité du client pour mettre à jour le MD5 chaque fois que le blob ou le fichier est modifié.

AzCopy affecte toujours la propriété contenu-MD5 pour un fichier ou blob Azure après le téléchargement du service.  

**Applicables aux :** Objets BLOB, fichiers

### <a name="snapshot"></a>/ Instantané

Indique si vous souhaitez transférer des instantanés. Cette option est valide uniquement lorsque la source est un blob.

Les instantanés blob transférés sont renommés au format suivant : .extension blob-name (instantané-heure)

Par défaut, des instantanés ne sont pas copiés.

**Applicables aux :** Objets BLOB

### <a name="vverbose-log-file"></a>/ V : [détaillée-fichier journal]

Messages d’état détaillés sorties dans un fichier journal.

Par défaut, le fichier journal détaillé est nommé AzCopyVerbose.log dans `%LocalAppData%\Microsoft\Azure\AzCopy`. Si vous spécifiez un emplacement du fichier existant pour cette option, le journal détaillé sera ajouté à ce fichier.  

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="zjournal-file-folder"></a>/ Z : [dossier de fichier journal]

Spécifie un dossier de fichier journal pour reprendre une opération.

AzCopy prend en charge les reprise si une opération a été interrompue.

Si cette option n’est pas spécifiée ou si elle est spécifié sans un chemin d’accès, AzCopy créer le fichier journal à l’emplacement par défaut, ce qui correspond à % LocalAppData%\Microsoft\Azure\AzCopy.

Chaque fois que vous exécutez une commande à AzCopy, elle vérifie si un fichier journal existe dans le dossier par défaut, ou s’il existe dans un dossier que vous avez spécifié via cette option. Si le fichier journal n’existe pas à cet emplacement, AzCopy traite l’opération en tant que nouveau et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande dans le fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération non terminée. Si elles ne correspondent pas, vous devrez soit remplacer le fichier journal pour démarrer une nouvelle opération, ou pour annuler l’opération en cours.

Le fichier journal est supprimé en cas de réussite de l’opération.

Notez que reprise d’une opération à partir d’un fichier journal créé par une version antérieure de AzCopy n’est pas pris en charge.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="parameter-file"></a>/@:"parameter-file"

Spécifie un fichier qui contient des paramètres. AzCopy traite les paramètres dans le fichier comme s’ils avaient été spécifiés dans la ligne de commande.

Dans un fichier de réponse, spécifier plusieurs paramètres sur une seule ligne ou spécifier chaque paramètre sur une ligne distincte. Notez qu’un paramètre individuel ne peut pas contenir plusieurs lignes.

Fichiers réponse peuvent inclure des lignes de commentaires qui commencent par le symbole « # ».

Vous pouvez spécifier plusieurs fichiers réponse. Toutefois, notez que AzCopy ne prend pas en charge les fichiers réponse imbriqués.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="y"></a>/ Y

Supprime toutes les invites de confirmation AzCopy.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="l"></a>/ L

Spécifie une opération de la liste uniquement ; aucune donnée n’est copiée.

AzCopy interprète l’à l’aide de cette option comme simulation pour l’exécution de la ligne de commande sans cette option/l et compter le nombre d’objets est copié, vous pouvez spécifier option /V en même temps pour vérifier les objets qui seront copiées dans le journal en clair.

Le comportement de cette option est également déterminé par l’emplacement de la source de données et la présence de le récursive option /S et fichier modèle option mode /Pattern.

AzCopy nécessite une autorisation de liste et de lecture de cet emplacement source lors de l’utilisation de cette option.

**Applicables aux :** Objets BLOB, fichiers

### <a name="mt"></a>/ MT

Définit l’heure de dernière modification du fichier téléchargé à être identique à celui du blob source ou du fichier.

**Applicables aux :** Objets BLOB, fichiers

### <a name="xn"></a>/XN

Exclut une ressource de code source plus récente. La ressource ne sera pas copiée si l’heure de dernière modification de la source est identique ou postérieure à destination.

**Applicables aux :** Objets BLOB, fichiers

### <a name="xo"></a>/XO

Exclut une ressource de code source plus ancienne. La ressource ne sera pas copiée si l’heure de dernière modification de la source est la même ou antérieurs à destination.

**Applicables aux :** Objets BLOB, fichiers

### <a name="a"></a>/A

Télécharge uniquement les fichiers qui ont l’attribut Archive.

**Applicables aux :** Objets BLOB, fichiers

### <a name="iarashcnetoi"></a>/ IA : [RASHCNETOI]

Télécharge uniquement les fichiers qui ont un des attributs spécifié est défini.

Attributs disponibles sont les suivantes :

- R = fichiers en lecture seule
- A = fichiers à archiver
- S = fichiers système
- H = fichiers cachés
- C = fichiers compressés
- N = fichiers Normal
- E = fichiers chiffré
- T = fichiers temporaires
- O = les fichiers en mode hors connexion
- J’ai = fichiers Non indexés

**Applicables aux :** Objets BLOB, fichiers

### <a name="xarashcnetoi"></a>/ XA : [RASHCNETOI]

Exclut les fichiers qui ont un des attributs spécifié est défini.

Attributs disponibles sont les suivantes :

- R = fichiers en lecture seule
- A = fichiers à archiver
- S = fichiers système
- H = fichiers cachés
- C = fichiers compressés
- N = fichiers Normal
- E = fichiers chiffré
- T = fichiers temporaires
- O = les fichiers en mode hors connexion
- J’ai = fichiers Non indexés

**Applicables aux :** Objets BLOB, fichiers

### <a name="delimiterdelimiter"></a>/ Délimiteur : « délimiteur »

Indique le caractère délimiteur utilisé pour délimiter répertoires virtuels dans un nom d’objets blob.

Par défaut, utilise AzCopy / comme caractère délimiteur. Toutefois, AzCopy prend en charge à l’aide de n’importe quel caractère courantes (tel que @, # ou %) comme un délimiteur. Si vous avez besoin d’inclure un des caractères spéciaux suivants sur la ligne de commande, placez le nom de fichier avec des guillemets doubles.

Cette option n’est applicable pour le téléchargement des objets BLOB.

**Applicables aux :** Objets BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/ NC : « nombre de simultané opérations »

Spécifie le nombre d’opérations simultanées.

AzCopy par défaut démarre un certain nombre d’opérations simultanées pour augmenter la vitesse de transfert de données. Notez que grand nombre d’opérations simultanées dans un environnement de faible bande passante peut surcharger la connexion réseau et empêcher l’opération de se terminer complètement. Limiter les opérations simultanées en fonction de la bande passante réseau réellement disponible.

La limite supérieure pour les opérations simultanées est 512.

**Applicables aux :** Tables des objets BLOB, fichiers,

### <a name="sourcetypeblob--table"></a>/ SourceType : « Blob » | « Tableau »

Spécifie que le `source` ressource est disponible dans l’environnement de développement local, en cours d’exécution dans l’émulateur de stockage blob.

**Applicables aux :** Objets BLOB, tableaux

### <a name="desttypeblob--table"></a>/ TypeDest : « Blob » | « Tableau »

Spécifie que le `destination` ressource est disponible dans l’environnement de développement local, en cours d’exécution dans l’émulateur de stockage blob.

**Applicables aux :** Objets BLOB, tableaux

### <a name="pkrskey1key2key3"></a>/ PKRS : « touche1 # Touche2 key3 #... »

Fractionner la plage de clés partition pour permettre l’exportation des données d’un tableau en parallèle, ce qui permet d’augmenter la vitesse de l’opération d’exportation.

Si cette option n’est pas spécifiée, AzCopy utilise un seul thread pour exporter des entités de table. Par exemple, si l’utilisateur spécifie /PKRS : « aa #bb », puis AzCopy démarre trois opérations simultanées.

Chaque opération exporte un des trois plages de clés partition, comme indiqué ci-dessous :

  [première clé de partition, aa)

  [aa, bb)

  [bb, clé de partition dernier]

**Applicables aux :** Tables

### <a name="splitsizefile-size"></a>/ SplitSize : « taille de fichier »

Spécifie le fichier exporté fractionner la taille en Mo, la valeur minimale autorisée est 32.

Si cette option n’est pas spécifiée, AzCopy exporter les données de la table à fichier unique.

Si les données du tableau sont exportées dans un blob, et la taille du fichier exporté atteint la limite de 200 Go pour la taille de blob, AzCopy fractionner le fichier exporté, même si cette option n’est pas spécifiée.

**Applicables aux :** Tables

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation : « InsertOrSkip » | « InsertOrMerge » | « InsertOrReplace »

Spécifie le comportement d’importation de données table.

- InsertOrSkip - permet d’ignorer une entité existante ou insère une nouvelle entité s’il n’existe pas dans la table.

- InsertOrMerge - fusionne une entité existante ou insère une nouvelle entité s’il n’existe pas dans la table.

- InsertOrReplace - remplace une entité existante ou insère une nouvelle entité s’il n’existe pas dans la table.

**Applicables aux :** Tables

### <a name="manifestmanifest-file"></a>/ Manifeste : « fichier de manifeste »

Spécifie le fichier manifeste pour la table d’exportation et l’opération d’importation.

Cette option est facultative pendant l’opération d’exportation, AzCopy génère un fichier manifeste avec nom prédéfini si cette option n’est pas spécifiée.

Cette option est nécessaire pendant l’opération d’importation pour trouver les fichiers de données.

**Applicables aux :** Tables

### <a name="synccopy"></a>/ SyncCopy

Indique s’il faut synchrone copier des objets BLOB ou des fichiers entre deux points de terminaison de stockage Azure.

AzCopy par défaut utilise copie asynchrone côté serveur. Spécifiez cette option pour effectuer une copie synchrone, qui télécharge des fichiers ou des objets BLOB vers la mémoire locale et puis les téléchargements au stockage Azure.

Vous pouvez utiliser cette option pour copier les fichiers au sein de stockage d’objets Blob, dans le stockage de fichiers, ou depuis le stockage Blob au stockage de fichiers ou vice versa.

**Applicables aux :** Objets BLOB, fichiers

### <a name="setcontenttypecontent-type"></a>/ SetContentType : « type de contenu »

Spécifie le type de contenu MIME pour des objets BLOB de destination ou de fichiers.

AzCopy définit le type de contenu pour un fichier ou blob application/octet-flux par défaut. Vous pouvez définir le type de contenu pour tous les objets BLOB ou des fichiers en spécifiant explicitement une valeur pour cette option.

Si vous spécifiez cette option sans une valeur, AzCopy définira chaque blob ou type de contenu du fichier en fonction de son extension de fichier.

**Applicables aux :** Objets BLOB, fichiers

### <a name="payloadformatjson--csv"></a>/ PayloadFormat : « JSON » | « CSV »

Spécifie le format du fichier de données exportées table.

Si cette option n’est pas spécifiée, AzCopy exporte fichier de données de tableau au format JSON par défaut.

**Applicables aux :** Tables

## <a name="known-issues-and-best-practices"></a>Problèmes connus et des pratiques recommandées

### <a name="limit-concurrent-writes-while-copying-data"></a>Limite des écritures simultanées lors de la copie de données

Lorsque vous copiez des objets BLOB ou des fichiers avec AzCopy, n’oubliez pas qu’une autre application peut modifier les données pendant que vous effectuez la copie. Si possible, assurez-vous que les données que vous effectuez la copie ne sont pas modifiées pendant l’opération de copie. Par exemple, lorsque vous copiez un disque dur virtuel associé à une machine virtuelle Azure, vérifiez qu’aucune autre application n’écrivez actuellement sur le disque dur virtuel. Une bonne façon de procéder est par location la ressource à copier. Vous pouvez également tout d’abord créer une capture instantanée de ce dernier, puis copiez l’instantané.

Si vous ne pouvez pas empêcher d’autres applications d’écrire sur des objets BLOB ou des fichiers pendant qu’ils sont en cours de copie, puis n’oubliez pas qu’au moment où que la tâche se termine, les ressources copiées ne peuvent ne plus avoir disparités qui existent complète avec les ressources de code source.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Exécuter une instance de AzCopy sur un ordinateur.
AzCopy est conçu pour optimiser l’utilisation de la ressource de votre ordinateur afin d’accélérer le transfert de données, nous vous recommandons de vous exécutez une instance de AzCopy sur un seul ordinateur et que vous spécifiez l’option `/NC` si vous avez besoin d’opérations plus simultanées. Pour plus d’informations, tapez `AzCopy /?:NC` à la ligne de commande.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Activer les algorithmes MD5 sont conformes pour AzCopy lorsque vous « utiliser FIPS algorithmes compatibles pour le chiffrement, hachage et la signature ».
AzCopy par défaut utilise implémentation .NET MD5 pour calculer la MD5 lors de la copie des objets, mais il existe des exigences de sécurité nécessitant AzCopy pour activer le paramètre MD5 compatible FIPS.

Vous pouvez créer un fichier app.config `AzCopy.exe.config` avec propriété `AzureStorageUseV1MD5` et placez-le côté avec AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Pour la propriété « AzureStorageUseV1MD5 » • vrai - la valeur par défaut, AzCopy utilisera implémentation MD5 .NET.
• Faux – AzCopy utilisera algorithmes MD5 compatibles FIPS.

Notez que les algorithmes sont conformes est désactivée par défaut sur votre ordinateur Windows, vous pouvez taper secpol.msc dans la fenêtre Exécuter et vérifier ce commutateur avec le paramètre sécurité -> stratégie locale-sécurité > Options -> chiffrement système : utilisation algorithmes sont conformes pour le chiffrement, hachage et la signature.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage Azure et AzCopy, consultez les ressources suivantes.

### <a name="azure-storage-documentation"></a>Documentation du stockage Azure :

- [Introduction au stockage Azure](storage-introduction.md)
- [L’utilisation de stockage d’objets Blob à partir de .NET](storage-dotnet-how-to-use-blobs.md)
- [Comment utiliser le stockage de fichiers à partir de .NET](storage-dotnet-how-to-use-files.md)
- [Comment utiliser le stockage de Table à partir de .NET](storage-dotnet-how-to-use-tables.md)
- [Comment créer, gérer ou supprimer un compte de stockage](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Billets de blog stockage Azure :
- [Présentation de stockage Azure données mouvement bibliothèque Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy : Présentation de copie synchrone et type de contenu personnalisé](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy : Annonce de la disponibilité général de AzCopy 3.0 plus de AzCopy 4.0 (version préliminaire) avec prise en charge de Table et le fichier](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy : Optimisé pour les scénarios copie à grande échelle](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy : Prise en charge pour le stockage geo redondants accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy : Transférer des données avec mode autorisant un redémarrage et jeton associations de sécurité](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy : À l’aide de compte de manière croisée copie Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy : Envoi/le téléchargement de fichiers pour les objets BLOB Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
