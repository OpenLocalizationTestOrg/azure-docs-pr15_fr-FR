<properties
    pageTitle="Migration vers Azure Premium stockage | Microsoft Azure"
    description="Migrer vos ordinateurs virtuels existants vers le stockage Azure Premium. Stockage Premium prend en charge disque High performance, faible latence je/O-accrue les charges de travail en cours d’exécution sur le Machines virtuelles Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>Migration vers stockage Azure Premium

## <a name="overview"></a>Vue d’ensemble

Le stockage Premium Azure offre prise en charge de disque hautes performances et à faible latence des machines virtuelles je/O-accrue les charges de travail en cours d’exécution. Vous pouvez tirer parti de la vitesse et les performances de ces disques en effectuant une migration disques de mémoire virtuelle de votre application pour le stockage Azure Premium.

L’objectif de ce guide est permettant de nouveaux utilisateurs de stockage de Premium Azure mieux préparer faciliter la transition à partir de leur système actuel au stockage Premium. Le guide traite de trois composants principaux dans ce processus de : 

  - [Planifier la Migration vers stockage Premium](#plan-the-migration-to-premium-storage)
  - [Préparer et copier des disques durs virtuels (disques durs virtuels) sur le stockage de Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [Créer des machines virtuelles Azure à l’aide de stockage Premium](#create-azure-virtual-machine-using-premium-storage)

Vous pouvez migrer des machines virtuelles à partir d’autres plateformes au stockage Premium Azure ou migrer des machines virtuelles Azure existantes à partir du stockage Standard au stockage Premium. Ce guide couvre les étapes pour les deux deux scénarios. Suivez la procédure indiquée dans la section appropriée en fonction de votre scénario.

>[AZURE.NOTE] Vous trouverez une présentation des fonctionnalités et les prix de stockage Premium dans stockage Premium : [Stockage High Performance pour charges de travail Azure Machine virtuelle](storage-premium-storage.md). Nous vous recommandons de migration n’importe quel disque machine virtuelle nécessitant des sorties par élevé au stockage Premium Azure pour optimiser les performances de votre application. Si votre disque ne nécessite pas sorties par haute, vous pouvez limiter les coûts en conservant dans stockage Standard, qui stocke les données de disque machine virtuelle sur disques durs (disques durs) au lieu de SSDs.

Fin du processus de migration dans son intégralité peut-être nécessiter des actions supplémentaires avant et après la procédure décrite dans ce guide. Exemples configurer réseaux virtuels ou les points de terminaison ou apporter des modifications du code l’application proprement dite qui peuvent nécessiter une interruption de service dans votre application. Ces actions sont propres à chaque application, et elles doivent être exécutées en même temps que la procédure décrite dans ce guide pour effectuer la transition complet au stockage Premium aussi transparente que possible.


## <a name="plan-the-migration-to-premium-storage"></a>Planifier la migration vers stockage Premium

Cette section garantit que vous êtes prêt à suivre les étapes de migration dans cet article et vous aide à prendre la meilleure décision sur les types de machine virtuelle et le disque.

### <a name="prerequisites"></a>Conditions préalables
- Vous avez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un abonnement mensuel [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/) ou visitez [Azure tarification](https://azure.microsoft.com/pricing/) pour plus d’options.
- Pour exécuter les applets de commande PowerShell, vous avez besoin du module Microsoft Azure PowerShell. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour les instructions d’installation et point d’installation.
- Lorsque vous prévoyez d’utiliser des machines virtuelles Azure exécuté sur un stockage Premium, vous devez utiliser les ordinateurs virtuels capables de stockage Premium. Vous pouvez utiliser des disques Standard et stockage Premium avec machines virtuelles capables de stockage Premium. Disques de stockage Premium sera disponibles avec davantage de types de machine virtuelle à l’avenir. Pour plus d’informations sur tous les types de disque Azure machine virtuelle et des formats disponibles, voir les [tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md)et les [tailles des machines virtuelles](../virtual-machines/virtual-machines-windows-sizes.md) .

### <a name="considerations"></a>Considérations relatives à la

Une machine virtuelle Azure prend en charge la connexion de plusieurs disques de stockage Premium afin que vos applications peuvent avoir jusqu'à 64 To de stockage par machine virtuelle. Grâce au stockage Premium, vos applications peuvent atteindre 80 000 par seconde (entrées/sorties par seconde) machine virtuelle et 2000 Mo débit par seconde disque par machine virtuelle avec extrêmement faible latence pour les opérations de lecture. Vous disposez des options dans une variété de machines virtuelles et des disques. Cette section consiste à vous aider à trouver une option qui correspond le mieux à votre charge de travail.

#### <a name="vm-sizes"></a>Taille de la mémoire virtuelle
Les spécifications de taille de la mémoire virtuelle Azure sont répertoriées dans les [tailles des machines virtuelles](../virtual-machines/virtual-machines-windows-sizes.md). Passez en revue les caractéristiques de performances des machines virtuelles qui fonctionnent avec stockage Premium et choisissez la taille de mémoire virtuelle plus appropriée qui correspond le mieux à votre charge de travail. Assurez-vous qu’il est suffisamment de bande passante disponible sur votre ordinateur virtuel à Attirez le disque.


#### <a name="disk-sizes"></a>Tailles de disque
Il existe trois types de disques pouvant être utilisés avec votre ordinateur virtuel et chaque a sorties par spécifiques et débit limites. Prendre en considération ces limites lorsque le choix du type de disque pour votre machine virtuelle en fonction des besoins de votre application en termes de capacité, performances et extensibilité élevées, et les charges maximum.

|Type de disque de stockage Premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Taille du disque|128 GO|512 GO|1 024 GO (1 TO)|
|Disque par seconde|500|2300|5000|
|Débit par disque|100 Mo par seconde|150 Mo par seconde|200 Mo par seconde|

En fonction de votre charge de travail, déterminez si disques de données supplémentaires sont nécessaires pour votre ordinateur virtuel. Vous pouvez joindre plusieurs disques de données permanente à votre ordinateur virtuel. Si nécessaire, vous pouvez répartir sur les disques pour augmenter la capacité et les performances du volume. (Voir qu’est agrégat [ici](storage-premium-storage-performance.md#disk-striping).) Si vous répartir disques de données de stockage Premium à l’aide des [Espaces de stockage][4], vous devez le configurer avec une colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume fractionné peuvent être inférieure à attendu en raison d’inégale du trafic entre les disques. Pour les machines virtuelles Linux, vous pouvez utiliser l’utilitaire *mdadm* pour obtenir les mêmes. Consultez l’article [Configurer logiciel RAID sous Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) pour plus d’informations.

#### <a name="storage-account-scalability-targets"></a>Cibles du extensibilité du compte de stockage
Comptes de stockage Premium ont cibles extensibilité élevées suivantes en plus [des objectifs de performances et extensibilité élevées de stockage de Azure](storage-scalability-targets.md). Si les besoins de votre application dépassent les cibles extensibilité élevées d’un compte de stockage unique, générer votre application pour utiliser plusieurs comptes de stockage et diviser vos données entre ces comptes de stockage.

|Capacité totale du compte|Bande passante totale pour un compte de stockage localement redondants|
|:--|:---|
|Capacité disque : 35 To<br />Capacité de capture instantanée : 10 To|Jusqu'à 50 Go par seconde pour entrant / sortant|

Pour plus d’informations sur les spécifications de stockage Premium, consultez [les objectifs de Performance lors de l’utilisation de stockage Premium et extensibilité élevées](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

#### <a name="disk-caching-policy"></a>Mise en cache de stratégie
Par défaut, la mise en cache de stratégie est *En lecture seule* pour toutes les données Premium disques et *En lecture / écriture* pour le disque système d’exploitation Premium associé à la machine virtuelle. Ce paramètre de configuration est recommandé d’obtenir des performances optimales pour votre application IOs. Pour les disques de données comportant beaucoup de l’écriture ou en écriture seule (par exemple, les fichiers journaux SQL Server), désactiver la mise en cache disque afin que vous pouvez obtenir de meilleures performances. Les paramètres de cache de disques de données existantes peuvent être mis à jour à l’aide du [Portail Azure](https://portal.azure.com) ou le paramètre *- HostCaching* de l’applet de commande *Set-AzureDataDisk* .

#### <a name="location"></a>Emplacement
Sélectionnez un emplacement où le stockage Azure Premium est disponible. Consultez [Services Azure par région](https://azure.microsoft.com/regions/#services) pour obtenir des informations sur les emplacements disponibles. Machines virtuelles situés dans la même région que le compte de stockage que banques de disques pour la machine virtuelle donne de meilleures performances que si elles sont en zones distinctes.

#### <a name="other-azure-vm-configuration-settings"></a>Autres paramètres de configuration machine virtuelle Azure
Lorsque vous créez une machine virtuelle Azure, vous devrez configurer certains paramètres de la machine virtuelle. N’oubliez pas, certains paramètres sont résolus pour la durée de vie de la machine virtuelle, tandis que vous pouvez modifier ou ajouter d’autres plus tard. Examinez ces paramètres de configuration machine virtuelle Azure et assurez-vous qu’ils sont correctement configurés conformément à vos besoins en matière de charge de travail.

### <a name="optimization"></a>Optimisation

[Le stockage azure Premium : création pour optimiser les performances](storage-premium-storage-performance.md) fournit des instructions pour la création d’applications High performance à l’aide de stockage Premium Azure. Vous pouvez suivre les instructions combinées avec les procédures recommandées applicables aux technologies utilisées par votre application.


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Préparer et copier des disques durs virtuels (disques durs virtuels) sur le stockage de Premium

La section suivante fournit des instructions pour préparer des disques durs virtuels à partir de votre machine virtuelle et copier des disques durs virtuels au stockage Azure.

- [Scénario 1 : « je suis migration machines virtuelles Azure existantes vers Azure Premium stockage. »](#scenario1)
- [Scénario 2 : « je suis migration machines virtuelles à partir d’autres plateformes vers Azure Premium stockage. »](#scenario2)

### <a name="prerequisites"></a>Conditions préalables

Pour préparer les disques durs virtuels pour la migration, vous devez :

- Un abonnement Azure, un compte de stockage et un conteneur dans ce compte de stockage à laquelle vous pouvez copier votre disque dur virtuel. Notez que le compte de stockage de destination peut être un compte Standard ou Premium stockage selon vos besoins.
- Outil généraliser le disque dur virtuel si vous prévoyez de créer plusieurs instances de machine virtuelle à partir de celui-ci. Par exemple, sysprep pour Windows ou pointeurs-sysprep Ubuntu.
- Outil pour télécharger le fichier de disque dur virtuel sur le compte de stockage. Voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) ou utilisez un [Explorateur de stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Ce guide décrit la copie de votre disque dur virtuel à l’aide de l’outil AzCopy.

> [AZURE.NOTE] Si vous choisissez l’option de copie synchrone avec AzCopy, pour optimiser les performances, copiez votre disque dur virtuel en exécutant un de ces outils à partir d’un ordinateur virtuel Azure qui se trouve dans la même région que le compte de stockage de destination. Si vous effectuez la copie un disque dur virtuel à partir d’une machine virtuelle Azure dans une zone différente, vos performances peuvent être plus lente.
>
> Pour copier une grande quantité de données sur bande passante limitée, vous pouvez [au moyen du service Azure importer/exporter pour transférer des données vers le stockage Blob](storage-import-export-service.md); Cela vous permet de transférer vos données en livraison des disques durs sur un centre de données Azure. Vous pouvez utiliser le service Azure importer/exporter pour copier les données dans un compte de stockage standard uniquement. Une fois les données dans votre compte de stockage standard, vous pouvez utiliser [l’API de Blob copie](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy pour transférer les données à votre compte de stockage premium.
>
> Notez que Microsoft Azure prend en charge les fichiers de disque dur virtuel de taille fixe. Fichiers VHDX ou disques durs virtuels dynamiques ne sont pas pris en charge. Si vous avez un disque dur virtuel dynamique, vous pouvez la convertir en taille fixe à l’aide de l’applet de commande de [Disque dur virtuel convertir](http://technet.microsoft.com/library/hh848454.aspx) .

### <a name="scenario1"></a>Scénario 1 : « je suis migration machines virtuelles Azure existantes vers Azure Premium stockage. »

Si vous migrez machines virtuelles Azure existantes, arrêter la machine virtuelle, préparer des disques durs virtuels par le type du disque dur virtuel souhaité et puis copiez le disque dur virtuel avec AzCopy ou PowerShell.

La machine virtuelle doit être complètement vers le bas pour déplacer un état vierge. Il y ait un temps d’arrêt jusqu'à ce que la migration terminée.

#### <a name="step-1-prepare-vhds-for-migration"></a>Étape 1. Préparer la migration disques durs virtuels

Si vous migrez machines virtuelles Azure existantes au stockage Premium, votre disque dur virtuel peut être :

- Une image de système d’exploitation GRG
- Un disque unique du système d’exploitation
- Un disque de données

Ci-dessous nous suivre ces 3 scénarios de préparation de votre disque dur virtuel.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Un disque dur virtuel système d’exploitation GRG permet de créer plusieurs instances de machine virtuelle

Si vous téléchargez un disque dur virtuel permettant de créer plusieurs instances machine virtuelle Azure génériques, vous devez tout d’abord generalize disque dur virtuel à l’aide d’un utilitaire sysprep. Cela s’applique à un disque dur virtuel qui se trouve en local ou sur le cloud. Sysprep supprime toutes les informations spécifiques à l’ordinateur à partir du disque dur virtuel.

>[AZURE.IMPORTANT] Prendre un instantané ou sauvegarder votre machine virtuelle avant la généralisation il. L’exécution de sysprep arrêtera et libérer de l’instance de machine virtuelle. Suivez les étapes ci-dessous pour sysprep un disque dur virtuel du système d’exploitation Windows. Notez que l’exécution de la commande Sysprep nécessite que vous arrêtez la machine virtuelle. Pour plus d’informations sur Sysprep, voir [Présentation de Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou [Référence technique Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Entrez la commande suivante pour ouvrir Sysprep :

        %windir%\system32\sysprep\sysprep.exe

3. Dans l’outil de préparation système, sélectionnez système Entrez de prédéfinies Experience (OOBE), activez la case à cocher Generalize, sélectionnez **Arrêter le système**, puis cliquez sur **OK**, comme illustré dans l’image ci-dessous. Sysprep sera généralise le système d’exploitation et arrêter le système.

    ![][1]

Pour un VM Ubuntu, utilisez pointeurs sysprep pour obtenir les mêmes. Consultez [pointeurs sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) pour plus d’informations. Voir aussi certaines des open source [logiciel de mise en service du serveur Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) pour d’autres systèmes d’exploitation Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utiliser un disque dur virtuel système d’exploitation unique pour créer une instance de machine virtuelle unique

Si vous avez une application en cours d’exécution sur l’ordinateur virtuel qui requiert les données machine, ne généralisez pas le disque dur virtuel. Un disque dur virtuel non généralisée pouvant être utilisé pour créer une instance de machine virtuelle Azure unique. Par exemple, si vous avez contrôleur de domaine sur votre disque dur virtuel, l’exécution de sysprep rend inefficace comme contrôleur de domaine. Passez en revue les applications s’exécutant sur votre ordinateur virtuel et l’impact de l’exécution de sysprep dessus avant la généralisation du disque dur virtuel.

##### <a name="register-data-disk-vhd"></a>Enregistrer les données disque dur

Si vous avez des disques de données dans Azure à migrer, vérifiez les ordinateurs virtuels qui utilisent ces disques de données ne sont pas arrêtés.

Suivez la procédure décrite ci-dessous pour copier le disque dur virtuel pour le stockage Azure Premium et enregistrer en tant qu’un disque de données généré.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Étape 2. Créez la destination de votre disque dur virtuel

Créer un compte de stockage pour maintenir vos disques durs virtuels. Lorsque vous planifiez l’emplacement de stockage de vos disques durs virtuels, tenez compte des points suivants :

- La cible du compte de stockage Premium.
- L’emplacement de stockage du compte doit être identique machines virtuelles Azure capable de stockage Premium vous allez créer dans la dernière étape. Vous pouvez copier vers un nouveau compte de stockage, ou envisagez d’utiliser le même compte de stockage selon vos besoins.
- Copiez et enregistrez la clé de compte de stockage du compte de stockage de destination pour l’étape suivante.

Pour les disques de données, vous pouvez choisir de conserver certains disques de données dans un compte de stockage standard (par exemple, des disques qui disposent de stockage REFROIDISSEUR), mais nous vous recommandons vivement de vous déplacer toutes les données de la charge de production à utiliser le stockage premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Étape 3. Copier le disque dur virtuel avec AzCopy ou PowerShell

Vous avez besoin trouver votre clé conteneur chemin d’accès et le stockage compte pour traiter une de ces deux options. Clé de compte de stockage et le chemin d’accès conteneur sont accessibles dans **Azure Portal** > **stockage**. L’URL de conteneur sera comme « https://myaccount.blob.core.windows.net/mycontainer/ ».

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Option 1 : Copier un disque dur virtuel avec AzCopy (copie asynchrone)

À l’aide de AzCopy, vous pouvez facilement télécharger le disque dur virtuel via Internet. Selon la taille des disques durs virtuels, cela peut prendre le temps. N’oubliez pas de vérifier les limites de pénétration/sortie de compte de stockage lorsque vous utilisez cette option. Pour plus d’informations, voir [extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md) .

1. Téléchargez et installez AzCopy à cet emplacement : [version la plus récente de AzCopy](http://aka.ms/downloadazcopy)
2. Ouvrez Azure PowerShell et accédez au dossier dans lequel AzCopy est installé.
3. Utilisez la commande suivante pour copier le fichier de disque dur virtuel de « Source » à « De Destination ».

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemple :

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    Voici une description des paramètres utilisés dans la commande AzCopy :

 - * */Source : * &lt;source&gt;:*** emplacement du dossier ou URL de conteneur de stockage qui contient le disque dur virtuel.
 - * */SourceKey : * &lt;clé de compte source&gt;:*** clé de compte de stockage du compte de stockage source.
 - * */Dest : * &lt;destination&gt;:*** URL de conteneur de stockage pour copier le disque dur virtuel à.
 - * */DestKey : * &lt;clé de compte dest&gt;:*** clé de compte de stockage du compte de stockage de destination.
 - * */Motif : * &lt;nom de fichier&gt;:*** spécifier le nom du fichier de ce dernier à copier.

Pour plus d’informations sur l’utilisation de AzCopy outil, voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Option 2 : Copier un disque dur virtuel avec PowerShell (copie synchronisée)

Vous pouvez également copier le fichier de disque dur virtuel à l’aide de l’applet de commande PowerShell démarrer AzureStorageBlobCopy. Utilisez la commande suivante sur Azure PowerShell pour copier le disque dur virtuel. Remplacer les valeurs de <> par les valeurs correspondantes de votre compte de stockage source et de destination. Pour utiliser cette commande, vous devez disposer d’un conteneur appelé disques durs virtuels dans votre compte de stockage de destination. Si le conteneur n’existe pas, créez-le avant d’exécuter la commande.

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

Exemple :

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>Scénario 2 : « je suis migration machines virtuelles à partir d’autres plateformes vers Azure Premium stockage. »

Si vous migrez disque dur virtuel à partir de stockage non Azure Cloud vers Azure, vous devez d’abord exporter le disque dur virtuel dans un répertoire local. Avoir le chemin d’accès source complet du répertoire local où le disque dur virtuel est stocké pratique et puis téléchargez-le sur le stockage Azure en utilisant AzCopy.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Étape 1. Exporter le disque dur virtuel dans un répertoire local

##### <a name="copy-a-vhd-from-aws"></a>Copiez un disque dur virtuel AWS

1. Si vous utilisez AWS, exporter l’instance EC2 sur un disque dur virtuel dans une plage Amazon S3. Suivez les étapes décrites dans la documentation Amazon pour l’exportation Amazon EC2 Instances installer l’outil d’interface de ligne (commande) EC2 Amazon et exécuter la commande instance-créer une tâche--exporter pour exporter l’instance EC2 dans un fichier de disque dur virtuel. Veillez à utiliser le **disque dur virtuel** pour le disque et #95 ; IMAGE & #95 ; Variable de FORMAT lors de l’exécution de la commande **Créer instance-exportation-de la tâche** . Le fichier de disque dur virtuel exporté est enregistré dans la plage de S3 Amazon que vous désignez pendant ce processus.

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. Téléchargez le fichier de disque dur virtuel à partir de la plage de S3. Sélectionnez le fichier de disque dur virtuel, puis **Actions** > **Télécharger**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copier un disque dur virtuel à partir d’autres cloud non Azure

Si vous migrez disque dur virtuel à partir de stockage non Azure Cloud vers Azure, vous devez d’abord exporter le disque dur virtuel dans un répertoire local. Copiez le chemin d’accès source complet du répertoire local l’emplacement de stockage de disque dur virtuel.

##### <a name="copy-a-vhd-from-on-premise"></a>Copiez un disque dur virtuel sur site

Si vous migrez disque dur virtuel à partir d’un environnement localement, vous devez le chemin d’accès source complet l’emplacement de stockage de disque dur virtuel. Le chemin d’accès source peut être un partage de fichier ou emplacement serveur.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Étape 2. Créez la destination de votre disque dur virtuel

Créer un compte de stockage pour maintenir vos disques durs virtuels. Lorsque vous planifiez l’emplacement de stockage de vos disques durs virtuels, tenez compte des points suivants :

- Le compte de stockage cible peut être standard ou premium stockage selon vos besoins d’application.
- La zone compte de stockage doit être identique machines virtuelles Azure capable de stockage Premium vous allez créer dans la dernière étape. Vous pouvez copier vers un nouveau compte de stockage, ou envisagez d’utiliser le même compte de stockage selon vos besoins.
- Copiez et enregistrez la clé de compte de stockage du compte de stockage de destination pour l’étape suivante.

Nous vous recommandons vivement de vous déplacer toutes les données de la charge de production à utiliser le stockage premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Étape 3. Télécharger le disque dur virtuel au stockage Azure

À présent que votre disque dur virtuel dans le répertoire local, vous pouvez utiliser AzCopy ou AzurePowerShell à télécharger le fichier .vhd au stockage Azure. Les deux options vous trouverez ici :

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Option 1 : À l’aide de PowerShell de Azure Add-AzureVhd à télécharger le fichier .vhd

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

Exemple <Uri> peuvent être **_« https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd »_**. Exemple <FileInfo> peuvent être **_« C:\path\to\upload.vhd »_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Option 2 : Utilisation de AzCopy pour télécharger le fichier .vhd

À l’aide de AzCopy, vous pouvez facilement télécharger le disque dur virtuel via Internet. Selon la taille des disques durs virtuels, cela peut prendre le temps. N’oubliez pas de vérifier les limites de pénétration/sortie de compte de stockage lorsque vous utilisez cette option. Pour plus d’informations, voir [extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md) .

1. Téléchargez et installez AzCopy à cet emplacement : [version la plus récente de AzCopy](http://aka.ms/downloadazcopy)
2. Ouvrez Azure PowerShell et accédez au dossier dans lequel AzCopy est installé.
3. Utilisez la commande suivante pour copier le fichier de disque dur virtuel de « Source » à « De Destination ».

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemple :

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    Voici une description des paramètres utilisés dans la commande AzCopy :

 - * */Source : * &lt;source&gt;:*** emplacement du dossier ou URL de conteneur de stockage qui contient le disque dur virtuel.
 - * */SourceKey : * &lt;clé de compte source&gt;:*** clé de compte de stockage du compte de stockage source.
 - * */Dest : * &lt;destination&gt;:*** URL de conteneur de stockage pour copier le disque dur virtuel à.
 - * */DestKey : * &lt;clé de compte dest&gt;:*** clé de compte de stockage du compte de stockage de destination.
 - **/BlobType : page :** Spécifie que la destination est un blob de page.
 - * */Motif : * &lt;nom de fichier&gt;:*** spécifier le nom du fichier de ce dernier à copier.

Pour plus d’informations sur l’utilisation de AzCopy outil, voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Autres options pour le téléchargement d’un disque dur virtuel

Vous pouvez également télécharger un disque dur virtuel à votre compte de stockage à l’aide d’une des méthodes suivantes :

- [Copie de stockage Azure Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Objets BLOB Azure stockage Explorer téléchargement](https://azurestorageexplorer.codeplex.com/)
- [Référence des API REST Service stockage importer/exporter](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] Nous vous recommandons d’à l’aide de Service d’importation/exportation si estimée le téléchargement de temps est plu de 7 jours. Vous pouvez utiliser [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pour estimer le temps à partir de l’unité de taille et de transfert de données.
>
> Importer/exporter peut être utilisé pour copier à un compte de stockage standard. Vous avez besoin copier du stockage standard pour le compte de stockage premium à l’aide d’un outil comme AzCopy.


## <a name="create-azure-virtual-machine-using-premium-storage"></a>Créer des ordinateurs virtuels Azure à l’aide de stockage Premium

Une fois que le disque dur virtuel est téléchargé ou copié dans le compte de stockage voulu, suivez les instructions fournies dans cette section pour enregistrer le disque dur virtuel comme une image du système d’exploitation, ou un disque du système d’exploitation en fonction de votre scénario, puis créez une instance de machine virtuelle à partir de celui-ci. Le disque dur de données peut être attaché à la machine virtuelle une fois qu’il est créé. Un exemple de script de migration est fournie à la fin de cette section. Ce script simple ne correspond pas à tous les scénarios. Vous devrez peut-être mettre à jour le script pour faire correspondre à votre scénario spécifique. Pour voir si ce script s’applique à votre scénario, voir ci-dessous [Un exemple de Script de Migration](#a-sample-migration-script).

### <a name="checklist"></a>Liste de vérification

1.  Attendez que tous les disques dur copie est terminée.
2.  Vérifiez que stockage Premium est disponible dans la région vers que vous migrez.
3.  Déterminez la nouvelle série de machine virtuelle que vous envisagez d’utiliser. Il doit être un système de stockage Premium capable et la taille doit être selon la disponibilité dans la région et selon vos besoins.
4.  Déterminez la taille exacte de mémoire virtuelle que vous allez utiliser. Taille de mémoire virtuelle doit être assez grande pour prendre en charge le nombre de disques de données que vous avez. Par exemple Si vous avez 4 disques de données, la machine virtuelle doivent cœurs, 2 ou plus. Envisagez également de mémoire la puissance de traitement et la bande passante réseau a besoin.
5.  Créer un compte de stockage Premium dans la région cible. C’est le compte que vous utiliserez pour la nouvelle machine virtuelle.
6.  Disposer les détails machine virtuelle actuel pratiques, y compris la liste des disques et des objets BLOB disque dur virtuel correspondantes.

Préparer votre application d’interruption de service. Pour effectuer une migration EPURAGE, vous devez arrêter le traitement dans le système actuel. Vous pouvez uniquement puis il accéder au cohérentes qui vous pouvez migrer vers la nouvelle plateforme. Temps d’arrêt dépend de la quantité de données dans les disques à migrer.

>[AZURE.NOTE] Si vous créez un gestionnaire de ressources Azure VM à partir d’un disque dur spécifique, reportez-vous à [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) pour le déploiement VM Gestionnaire de ressources à l’aide de disque existant.

### <a name="register-your-vhd"></a>Enregistrer votre disque dur virtuel

Pour créer une machine virtuelle à partir du disque dur virtuel du système d’exploitation ou pour joindre un disque de données à un nouvel ordinateur virtuel, vous devez tout d’abord les inscrire. Suivez les étapes ci-après selon le scénario de votre disque dur virtuel.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalized disque dur virtuel de système d’exploitation pour créer plusieurs instances de machine virtuelle Azure

Une fois le téléchargement de l’image du système d’exploitation GRG disque dur virtuel pour le compte de stockage, l’enregistrer en tant qu' **Image de machine virtuelle Azure** afin que vous pouvez créer une ou plusieurs instances de machine virtuelle à partir de celui-ci. Utiliser les applets de commande PowerShell suivante pour enregistrer votre disque dur virtuel sous forme d’image du système d’exploitation machine virtuelle Azure. Indiquez l’URL complète conteneur où le disque dur virtuel a été copié à.

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copiez et enregistrer le nom de cette nouvelle Image machine virtuelle Azure. Dans l’exemple ci-dessus, il est *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disque dur virtuel de système d’exploitation unique pour créer une instance de machine virtuelle Azure unique

Une fois que le disque dur virtuel OS uniques est téléchargé sur le compte de stockage, enregistrer en tant qu’un **Disque de système d’exploitation Azure** afin que vous pouvez créer une instance de machine virtuelle à partir de celui-ci. Utilisez ces applets de commande PowerShell pour inscrire votre disque dur virtuel comme un disque de système d’exploitation Azure. Indiquez l’URL complète conteneur où le disque dur virtuel a été copié à.

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copiez et enregistrer le nom de ce nouveau disque du système d’exploitation Azure. Dans l’exemple ci-dessus, il est *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Données de disque dur à joindre à nouveau ou des instances de machine virtuelle Azure

Une fois que le disque dur de données être téléchargé vers un compte de stockage, enregistrer en tant qu’un disque de données Azure afin qu’il peut être attaché à votre nouvelle série de service d’annuaire, DSv2 série ou instance GS série Azure machine virtuelle.

Utilisez ces applets de commande PowerShell pour inscrire votre disque dur virtuel comme un disque de données Azure. Indiquez l’URL complète conteneur où le disque dur virtuel a été copié à.

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copiez et enregistrer le nom de ce nouveau disque de données Azure. Dans l’exemple ci-dessus, il est *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Créer une machine virtuelle capable de stockage Premium

Une fois l’image du système d’exploitation ou disque du système d’exploitation sont enregistrés, créez une nouvelle de série de service d’annuaire, une série de DSv2 ou machine virtuelle GS série. Vous utiliserez l’image de système d’exploitation ou le nom du disque système d’exploitation que vous avez enregistré. Sélectionnez le type de machine virtuelle dans le niveau de stockage Premium. Dans l’exemple ci-dessous, nous utilisons la taille de la mémoire virtuelle *Standard_DS2* .

>[AZURE.NOTE] Mettre à jour la taille de disque pour vous assurer qu’elle corresponde à votre capacité et des exigences de performances et les tailles de disque Azure disponible.

Suivez les applets de commande étape par étape PowerShell ci-dessous pour créer la nouvelle machine virtuelle. Tout d’abord, définissez les paramètres courants :

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

Tout d’abord, créez un service cloud dans lequel vous hébergez vos nouvelles machines virtuelles.

    New-AzureService -ServiceName $serviceName -Location $location

Ensuite, en fonction de votre scénario, créez l’instance Azure machine virtuelle à partir de l’Image du système d’exploitation ou un disque du système d’exploitation que vous avez enregistré.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalized disque dur virtuel de système d’exploitation pour créer plusieurs instances de machine virtuelle Azure

Créer une ou plusieurs nouvelles DS série Azure machine virtuelle instances à l’aide de l' **Image de système d’exploitation Azure** que vous avez enregistré. Spécifiez ce nom de l’Image du système d’exploitation dans la configuration de la machine virtuelle lors de la création de nouvelle machine virtuelle comme illustré ci-dessous.

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disque dur virtuel de système d’exploitation unique pour créer une instance de machine virtuelle Azure unique

Créer une nouvelle instance de machine virtuelle Azure de série DS à l’aide du **Disque OS Azure** que vous avez enregistré. Spécifiez ce nom de disque du système d’exploitation sur la configuration de la machine virtuelle lors de la création de la nouvelle machine virtuelle comme illustré ci-dessous.

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

Spécifiez d’autres informations de machine virtuelle Azure, par exemple un service cloud, région, compte de stockage, jeu de disponibilité et stratégie de mise en cache. Notez que l’instance machine virtuelle doit être situé au même endroit avec le système d’exploitation associé ou disques de données, afin que le compte de service, de région et de stockage cloud sélectionnée doit être dans le même emplacement que les durs sous-jacentes de ces disques.

### <a name="attach-data-disk"></a>Joindre disque de données

Enfin, si vous avez enregistré données disques durs, associez-les à la nouvelle stockage Premium avec Azure machine virtuelle.

Utilisez la suite applet de commande PowerShell pour attacher des données disque à la nouvelle machine virtuelle et spécifier la stratégie de mise en cache. Dans l’exemple ci-dessous, la stratégie de mise en cache est définie sur *en lecture seule*.

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] Il peut y avoir des étapes supplémentaires pour prendre en charge de votre application qui se trouve ne pas couverts par ce guide.

### <a name="checking-and-plan-backup"></a>Plan de sauvegarde et de vérification

Une fois que la nouvelle machine virtuelle est en cours d’exécution, y accéder en utilisant le même id de connexion et votre mot de passe est en tant que la machine virtuelle d’origine et vérifier que tout fonctionne comme prévu. Tous les paramètres, y compris les volumes fractionnés, est présents dans la nouvelle machine virtuelle.

La dernière étape consiste à planifier la sauvegarde et planification de la maintenance pour la nouvelle machine virtuelle en fonction des besoins de l’application.

### <a name="a-sample-migration-script"></a>Un exemple de script de migration

Si vous avez plusieurs ordinateurs virtuels pour migrer, automatisation via des scripts PowerShell seront utile. Voici un exemple de script permettant d’automatiser la migration d’un ordinateur virtuel. Notez que sous script n'est qu’un exemple et il existe quelques hypothèses relatives aux disques machine virtuelle en cours. Vous devrez peut-être mettre à jour le script pour faire correspondre à votre scénario spécifique.

Les hypothèses sont :

- Vous créez machines virtuelles Azure classique.
- Votre source du système d’exploitation disques et données source sont dans le même compte de stockage et même conteneur. Si votre système d’exploitation disques et données ne sont pas au même endroit, vous pouvez utiliser AzCopy ou PowerShell Azure durs écrasement des conteneurs et des comptes de stockage. Reportez-vous à l’étape précédente : [Disque dur virtuel de copie avec AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). Ce script pour répondre à votre scénario d’édition est un autre choix, mais nous vous recommandons d’utiliser AzCopy ou PowerShell car il est plus simple et rapide.

Le script automation est fourni ci-dessous. Remplacer du texte avec vos informations et mettre à jour le script pour faire correspondre à votre scénario spécifique.

>[AZURE.NOTE] En utilisant le script existant ne conserve pas la configuration du réseau de votre source de machine virtuelle. Vous devrez ré-config les paramètres de mise en réseau sur vos ordinateurs virtuels migrés.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>Optimisation

La configuration actuelle de la machine virtuelle peut être personnalisée spécifiquement pour fonctionner avec des disques Standard. Par exemple, pour améliorer les performances à l’aide de nombreux disques dans un volume fractionné. Par exemple, au lieu d’utiliser 4 disques séparément sur le stockage Premium, vous pourrez peut-être optimiser le coût en demandant à un seul disque. Optimisations comme ce doivent être gérés sur des cas par cas et nécessiter des étapes personnalisées après la migration. Notez également que ce processus ne fonctionnent pas correctement avec des bases de données et des applications qui dépendent de la disposition du disque définie dans le programme d’installation.

##### <a name="preparation"></a>Préparation

1.  Effectuer la Migration Simple comme décrit dans la section précédente. Optimisations seront effectuées sur la nouvelle machine virtuelle après la migration.
2.  Définir les nouvelles tailles de disque nécessaires pour la configuration optimisée.
3.  Déterminer le mappage des disques/volumes en cours pour les nouvelles spécifications de disque.

##### <a name="execution-steps"></a>Étapes de l’exécution

1.  Créer les nouveaux disques avec les tailles droit sur l’ordinateur virtuel stockage Premium.
2.  Connectez-vous à la machine virtuelle et copier les données à partir du volume actuel vers le nouveau disque qui correspond à ce volume. Procédez comme suit pour tous les volumes actuels doivent mapper vers un nouveau disque.
3.  Ensuite, modifiez les paramètres d’application pour basculer vers les nouveaux disques et détacher des anciens volumes.

Pour optimiser l’application pour de meilleures performances de disque, reportez-vous à [Optimisation des performances de l’Application](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrations d’application

Bases de données et d’autres applications complexes peuvent nécessiter des étapes spéciales telle que définie par le fournisseur d’application pour la migration. Reportez-vous à la documentation de l’application respective. Par exemple généralement les bases de données pouvant être migrées via la sauvegarde et de restauration.

## <a name="next-steps"></a>Étapes suivantes

Consultez les ressources suivantes pour des scénarios spécifiques pour la migration de machines virtuelles :

- [Migrer Azure Machines virtuelles entre des comptes de stockage](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Créer et télécharger un disque dur virtuel du serveur de Windows Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Création et le téléchargement d’un disque dur virtuel qui contient le système d’exploitation Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Migration des Machines virtuelles à partir d’Amazon AWS à Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

En outre, consultez les ressources suivantes pour en savoir plus sur le stockage Azure et Machines virtuelles Azure :

- [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [: Premium High Performance de stockage pour les charges de travail Azure Machine virtuelle](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
