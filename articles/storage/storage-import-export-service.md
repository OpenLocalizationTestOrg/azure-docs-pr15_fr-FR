<properties
    pageTitle="À l’aide d’importation/exportation pour transférer des données vers le stockage Blob | Microsoft Azure"
    description="Apprenez à créer importer et exporter des tâches dans le portail pour transférer des données pour le stockage blob Azure classique."
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Utiliser le Service Microsoft Azure importer/exporter pour transférer des données vers le stockage d’objets Blob

## <a name="overview"></a>Vue d’ensemble

Service d’importation/exportation Azure permet de transférer en toute sécurité de grandes quantités de données à Azure blob storage par livraison lecteurs de disques durs à un centre de données Azure. Vous pouvez également utiliser ce service pour transférer des données depuis le stockage blob Azure aux lecteurs de disques durs et livrer à votre site local. Ce service convient dans les situations où vous souhaitez transférer plusieurs to de données vers ou à partir d’Azure, mais le chargement ou le téléchargement du réseau n’est pas possible en raison de bande passante limitée ou sur les coûts de réseau élevé.

Le service nécessite que les lecteurs de disques durs doivent être verrouillage bit chiffré pour la sécurité de vos données. Le service prend en charge les comptes de stockage classique présents dans toutes les régions du Public Azure. Vous devez livrer des disques durs à un des emplacements pris en charge spécifiés plus loin dans cet article.

Dans cet article, vous apprendrez plus sur le service Azure importer/exporter et comment envoyer des lecteurs de copie de vos données vers et depuis le stockage Blob Azure.

> [AZURE.IMPORTANT] Vous pouvez créer et gérer l’importation et exportation des travaux à l’aide du portail classique ou le [service d’importation/exportation API REST](http://go.microsoft.com/fwlink/?LinkID=329099)de stockage classique. Comptes de stockage directeur des ressources ne sont pas prises en charge pour le moment.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Quand dois-je utiliser le service d’importation/exportation Azure ?

Vous pouvez envisager d’utiliser service Azure importer/exporter lors du chargement ou téléchargement des données sur le réseau est trop lent ou l’obtention de bande passante réseau supplémentaire est coûteux.

Vous pouvez utiliser ce service dans les scénarios tels que :

- Migration des données dans le cloud : déplacer rapidement de grandes quantités de données vers Azure et de rentabilité.
- Distribution de contenu : envoyer rapidement des données à vos sites client.
- Sauvegarde : Effectuer des sauvegardes de vos données en local pour stocker dans le stockage blob Azure.
- Récupération des données : récupérer grande quantité de données stockées dans le stockage blob et faites-lui remis à votre emplacement local.

## <a name="pre-requisites"></a>Conditions préalables

Dans cette section, nous avons répertorié les conditions préalables requises pour utiliser ce service. Veuillez vérifier les avec soin avant d’expédition de vos lecteurs.

### <a name="storage-account"></a>Compte de stockage

Vous devez disposer d’un abonnement Azure existant et un ou plusieurs des comptes de stockage **classique** pour utiliser le service d’importation/exportation. Chaque tâche peut servir à transférer des données vers ou à partir d’un seul compte de stockage classique. En d’autres termes, une tâche unique importer/exporter ne comprennent pas entre plusieurs comptes de stockage. Pour plus d’informations sur la création d’un nouveau compte de stockage, voir [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Types d’objets BLOB

Vous pouvez utiliser le service Azure importer/exporter pour copier des données à des objets BLOB **bloc** ou des objets BLOB de **Page** . À l’inverse, vous pouvez exporter uniquement des objets BLOB **bloc** , des objets BLOB de **Page** ou des objets BLOB **Ajout** du stockage Azure à l’aide de ce service.

### <a name="job"></a>Tâche

Pour commencer le processus d’importation et l’exportation depuis le stockage Blob, vous créez tout d’abord une tâche. Une tâche peut être une tâche d’importation ou exportation du travail :

- Créer une tâche d’importation lorsque vous souhaitez transférer des données que vous avez local pour des objets BLOB dans votre compte de stockage Azure.
- Créer une tâche d’exportation lorsque vous souhaitez transférer des données stockées actuellement en tant que BLOB dans votre compte de stockage pour des disques durs qui sont fournis pour vous.

Lorsque vous créez une tâche, vous avertir le service d’importation/exportation que vous seront disponibles à un ou plusieurs disques durs à un centre de données Azure.

- Pour une tâche d’importation, vous seront disponibles disques durs contenant vos données.
- Pour une tâche d’exportation, vous seront disponibles disques durs vides.
- Vous pouvez livrer jusqu'à 10 disques durs par tâche.

Vous pouvez créer une importation ou exportation de travail à l’aide du [portail classique](https://manage.windowsazure.com/) ou l' [API REST de Azure stockage importer/exporter](http://go.microsoft.com/fwlink/?LinkID=329099).

### <a name="client-tool"></a>Outil client

La première étape pour créer une tâche **d’importation** est pour préparer votre lecteur qui est expédié à importer. Pour préparer votre lecteur, vous devez vous connecter à un serveur local et exécuter l’outil Azure importer/exporter Client sur le serveur local. Cet outil client facilite la copie de vos données sur le lecteur, le chiffrement des données sur le disque avec BitLocker et générer les fichiers de feuille de lecteur.

Les fichiers journaux stockent les informations de base sur votre travail et un lecteur tels que le numéro de série de lecteur et le nom de compte de stockage. Ce fichier journal n’est pas stocké sur le disque. Il est utilisé lors de la création de travail importer. Étape par étape plus d’informations sur la création de travail sont fournies plus loin dans cet article.

L’outil client n’est pas compatible avec les systèmes d’exploitation 64 bits Windows. Consultez la section [système d’exploitation](#operating-system) pour des versions spécifiques du système d’exploitation pris en charge.

Téléchargez la dernière version de l' [outil de client Azure importer/exporter](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Pour plus d’informations sur l’utilisation de l’outil d’importation/exportation Azure, voir la [Référence d’outils Azure importer/exporter](http://go.microsoft.com/fwlink/?LinkId=329032).

### <a name="hard-disk-drives"></a>Lecteurs de disques durs

3,5 pouces uniquement des disques durs internes SATA II/III sont prises en charge pour une utilisation avec le service d’importation/exportation. Vous pouvez utiliser des disques durs jusqu'à 10 To.
Pour les tâches d’importation, uniquement le premier volume de données sur le disque est traité. Le volume de données doit être au format NTFS.
Lorsque vous copiez des données sur votre disque dur, vous pouvez attacher directement à l’aide d’un connecteur SATA ou vous pouvez joindre l’extérieur à l’aide d’un adaptateur SATA II/III USB externe. Nous recommandons d’utiliser une des cartes SATA II/III USB externes suivants :

- Anker 68UPSATAA - 02 unité commerciale
- Anker 68UPSHHDS-unité commerciale
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Si vous avez un convertisseur qui n’est pas répertorié ci-dessus, vous pouvez essayer d’exécuter l’outil d’importation/exportation Azure à l’aide de votre convertisseur pour préparer le disque et voyez si cela fonctionne avant d’acheter un convertisseur pris en charge.

> [AZURE.IMPORTANT] Disques durs externes fournis avec un adaptateur USB intégré ne sont pas prises en charge par ce service. En outre, le disque à l’intérieur de la casse d’un disque dur externe ne peut pas être utilisé ; n’envoyez pas de disques durs externes.

### <a name="encryption"></a>Chiffrement

Les données sur le disque doivent être chiffrées à l’aide de chiffrement de lecteur BitLocker. Cela protège vos données bien qu’il soit lors des transferts.

Pour les tâches d’importation, il existe deux façons d’effectuer le chiffrement. La première consiste à utiliser la / chiffrer paramètre lors de l’exécution de l’outil client lors de la préparation du lecteur. La deuxième consiste à activer le chiffrement BitLocker manuellement sur le disque et indiquer la clé de chiffrement dans la ligne de commande client outil lors de la préparation du lecteur.

Pour exporter les tâches, une fois que vos données sont copiées sur les lecteurs, le service chiffrer le lecteur à l’aide de BitLocker avant livraison avec vous. La clé de chiffrement est fournie vous via le portail classique.  

### <a name="operating-system"></a>Système d'exploitation

Vous pouvez utiliser un des systèmes d’exploitation 64 bits suivants pour préparer le disque dur à l’aide de l’outil d’importation/exportation Azure avant le lecteur sur Azure d’expédition :

Windows 7 entreprise, intégrale de Windows 7, Windows 8 Pro, entreprise de Windows 8, Windows 8.1 Pro, entreprise Windows 8.1, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tous ces systèmes d’exploitation prennent en charge le chiffrement de lecteur BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup> Si vous utilisez un ordinateur Windows 10 pour préparer votre disque dur, téléchargez la dernière version de l’outil d’importation/exportation Azure.

### <a name="locations"></a>Emplacements

Le service Azure importer/exporter prend en charge la copie des données vers et depuis tous les comptes de stockage Public Azure. Vous pouvez fournir des disques durs à un des emplacements suivants. Si votre compte de stockage est dans un lieu public Azure qui n’est pas spécifié ici, un emplacement d’expédition substitution est fourni lorsque vous créez la tâche à l’aide du portail classique ou l’API REST importer/exporter.

Prise en charge des lieux d’expédition :

- États-Unis Extrême-Orient

- États-Unis Ouest

- États-Unis Extrême-Orient 2

- États-Unis centre

- États-Unis centre nord

- États-Unis centre sud

- Europe du Nord

- Europe occidentale

- Asie de l’est

- Pays d’Asie du Sud-est

- Australie est

- Australie sud-est.

- Japon ouest

- Orient Japon

- Inde central


### <a name="shipping"></a>Livraison

**Lecteurs de livraison au centre de données :**

Lorsque vous créez une tâche d’importation ou d’exportation, vous trouverez une adresse d’expédition d’un des emplacements pris en charge pour envoyer vos lecteurs. L’adresse de livraison fourni dépend de l’emplacement de votre compte de stockage, mais il peut ne pas être identique à votre emplacement de compte de stockage.

Vous pouvez utiliser les opérateurs s’affiche comme FedEx, DHL, alimentation de secours ou le Service Postal US pour envoyer vos lecteurs à l’adresse de livraison.

**Lecteurs de livraison à partir du centre de données :**

Lorsque vous créez une tâche d’importation ou d’exportation, vous devez fournir une adresse d’expéditeur Microsoft à utiliser lors de l’expédition les lecteurs une fois que votre tâche est terminée. Vérifiez que vous avez défini une adresse d’expéditeur valide pour éviter des délais traitement.

Vous devez également fournir un support FedEx ou DHL valide numéro devant être utilisé par Microsoft pour les lecteurs précédent d’expédition de compte. Un numéro de compte FedEx est requis pour les lecteurs d’expédition retour à partir des emplacements États-Unis et Europe. Un numéro de compte DHL est requis pour les lecteurs d’expédition revenir à partir d’emplacements Asie et Australie. Si vous n’en avez pas, vous pouvez créer un compte de transporteur [DHL](http://www.dhl.com/) (Asie et Australie) ou de [FedEx](http://www.fedex.com/us/oadr/) (pour les États-Unis et Europe). Si vous avez déjà un numéro de compte transporteur, vérifiez qu’elle est valide.

Dans vos packages d’expédition, vous devez suivre les termes en [Termes de Service Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Veuillez noter que le média physique que vous fournissez doivent croisées bordures internationales. Vous êtes chargé de s’assurer que vos supports physiques et des données sont importées et/ou exportées en respectant la législation applicable. Avant le média physique d’expédition, vérifiez auprès de votre conseillers pour vérifier que vos données et éléments multimédias peuvent légalement être expédiées au centre de données identifié. Cela vous aidera à vérifier qu’il connecte à Microsoft rapidement. Par exemple, un package qui traversent les frontières internationales a besoin d’une facture commerciale soit accompagnée avec le package (sauf si qui traversent bordures au sein de l’Union européenne). Vous pouvez imprimer une copie de la facture commerciale à partir du site Web de support remplie. Exemple de commerciale facture sont [facture commerciale DHL] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) ou une [Facture commerciale FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Assurez-vous que Microsoft n’a pas été indiquée comme l’exporter.

## <a name="how-does-the-azure-importexport-service-work"></a>Comment fonctionne le service d’importation/exportation Azure ?

Vous pouvez transférer des données entre votre site local et le stockage blob Azure au moyen du service Azure importer/exporter en créant des travaux et des disques durs à un centre de données Azure d’expédition. Chaque lecteur de disque dur que vous livrez est associé à une seule tâche. Chaque tâche est associée à un compte de stockage unique. Passez en revue la [section conditions préalables](#pre-requisites) avec soin pour en savoir plus sur les caractéristiques de ce types d’objets blob service tel que pris en charge, des types de disque, emplacements et livraison.

Dans cette section, nous décrira à un niveau élevé les étapes nécessaires pour importer et exporter des tâches. Plus loin dans la [section de démarrage rapide](#quick-start), nous fournissons des instructions détaillées pour créer une importation et exportation de travail.

### <a name="inside-an-import-job"></a>À l’intérieur d’une tâche d’importation

À un niveau élevé, une tâche d’importation implique les étapes suivantes :

- Déterminer les données à importer et le nombre de lecteurs que vous aurez besoin.
- Identifier les objets BLOB destination pour vos données dans le stockage Blob.
- Utilisez l’outil d’importer/exporter Azure pour copier les données dans un ou plusieurs disques durs et les chiffrer avec BitLocker.  
- Créer une tâche d’importation dans votre compte de stockage classique cible à l’aide du portail classique ou l’API REST importer/exporter. Si vous utilisez le portail classique, téléchargez les fichiers de feuille de lecteur.
- Indiquez l’adresse de l’expéditeur et le nombre de compte transporteur à utiliser pour les lecteurs d’expédition avec vous.
- Envoyer les lecteurs de disques durs à l’adresse de livraison fournie lors de la création de la tâche.
- Mettre à jour la remise numéro dans les détails de la tâche Importation de suivi et soumettre l’importation.
- Lecteurs de réception et du traitement au centre de données Azure.
- Unités sont envoyées à l’aide de votre compte de support à l’adresse d’expéditeur fournie dans la tâche d’importation.

    ![Flux de travail figure 1:Import](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>À l’intérieur d’une tâche d’exportation

À un niveau élevé, une tâche d’exportation implique les étapes suivantes :

- Déterminer les données à exporter et le nombre de lecteurs que vous aurez besoin.
- Identifier les objets BLOB source ou les chemins d’accès du conteneur de vos données dans le stockage Blob.
- Créer une tâche d’exportation dans votre compte de stockage source à l’aide du portail classique ou l’API REST importer/exporter.
- Spécifiez les objets BLOB source ou les chemins d’accès du conteneur de vos données dans la tâche d’exportation.
- Fournir l’adresse de l’expéditeur et le nombre de compte transporteur pour à être utilisé pour les lecteurs d’expédition avec vous.
- Envoyer les lecteurs de disques durs à l’adresse de livraison fournie lors de la création de la tâche.
- Mettre à jour la remise numéro dans les détails d’une tâche exportation de suivi et soumettre la tâche d’exportation.
- Les lecteurs de réception et du traitement au centre de données Azure.
- Les lecteurs chiffrés avec BitLocker ; les touches sont disponibles via le portail classique.  
- Les unités sont envoyées à l’aide de votre compte de support à l’adresse d’expéditeur fournie dans la tâche d’importation.

    ![Flux de travail figure 2:Export](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>Affichage de votre statut de la tâche

Vous pouvez suivre l’état de votre importation ou exportation des travaux à partir du portail classique. Accédez à votre compte de stockage dans le portail classique, puis cliquez sur l’onglet **Importer/exporter** . Une liste de vos projets s’affichent dans la page. Vous pouvez filtrer la liste sur le statut de la tâche, nom de la tâche, type de tâche ou numéro de suivi.

Vous verrez un des statuts de travail suivants en fonction de l’endroit où votre lecteur figure dans le processus.

| État du travail   | Description                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Création     | Votre travail a été créé, mais vous n’avez pas encore fourni vos informations d’expédition.                                                                                                                                                                                                                                                                                                |
| Livraison     | Votre travail a été créé et que vous avez fourni vos informations d’expédition. **Remarque**: lorsque le lecteur est remis dans le centre de données Azure, l’état peut indiquent toujours « Expédition » depuis un certain temps. Une fois que le service démarre la copie de vos données, le statut devient « Transfert ». Si vous voulez afficher état plus spécifique de votre disque, vous pouvez utiliser l’API REST importer/exporter. |
| Transfert | Vos données sont transférées à partir de votre disque dur (pour une tâche d’importation) ou sur votre disque dur (pour une tâche d’exportation).                                                                                                                                                                                                                                                                 |
| Création d’un package    | Le transfert de vos données est terminé, et votre disque dur est en cours de préparation pour la livraison avec vous.                                                                                                                                                                                                                                                                             |
| Terminer     | Votre disque dur a été livré avec vous.                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>Temps de travail de processus

La durée nécessaire au traitement d’une opération d’importation/exportation varie en fonction de différents facteurs tels que délai d’expédition, la fonction type, type et taille des données en cours de copie et la taille des disques fourni. Le Service d’importation/exportation n’a pas d’accord. Vous pouvez utiliser l’API REST pour suivre l’avancement de tâche plus près. Il existe un paramètre pourcentage achevé dans l’opération de répertorier les tâches qui donne une indication de progression de la copie. Restez en contact nous si vous avez besoin d’une estimation pour terminer une tâche critique importation/exportation de temps.

### <a name="pricing"></a>Tarifs

**Lecteur frais d’expédition**

Il y a des frais de gestion lecteur pour chaque lecteur traitée dans le cadre de votre importation ou de la tâche d’exportation. Afficher les détails sur les [Tarifs de Azure importer/exporter](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Frais d’expédition**

Lorsque vous propose des disques vers Azure, vous payez le coût d’expédition au transporteur. Lorsque Microsoft renvoie les lecteurs pour vous, le coût d’expédition est facturé sur le compte carrier que vous fourni au moment de la création d’une tâche.

**Coûts de transaction**

Il n’existe aucun coût transaction lors de l’importation de données dans le stockage blob. Les frais de sortie standard sont appliquent lorsque les données sont exportées depuis le stockage blob. Pour plus d’informations sur les coûts de transaction, voir [de transfert de données tarifs.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Démarrage rapide

Dans cette section, nous fournissons des instructions détaillées pour la création d’une importation et une tâche d’exportation. Vérifiez que vous remplissez toutes les [conditions préalables](#pre-requisites) avant de passer.

## <a name="how-to-create-an-import-job"></a>Comment créer une tâche d’importation ?

Créer une tâche d’importation pour copier les données à votre compte de stockage Azure des disques durs par livraison un ou plusieurs lecteurs contenant les données du centre de données spécifiée. La tâche d’importation transmet plus d’informations sur les lecteurs de disques durs, les données copiées, cibler compte de stockage et les informations d’expédition au service Azure importer/exporter. Création d’une tâche d’importation est un processus en trois étapes. Commencez par préparer vos lecteurs à l’aide de l’outil de client Azure importer/exporter. Ensuite, envoyer une tâche d’importation à l’aide du portail classique. Troisième livrer les lecteurs à l’adresse de livraison fournie lors de la création de la tâche et mettre à jour les informations de livraison dans votre détails d’une tâche.   

> [AZURE.IMPORTANT] Vous pouvez envoyer uniquement une tâche par le compte de stockage. Chaque lecteur que vous fournies peut être importé dans un seul compte de stockage. Par exemple, supposons que vous souhaitez importer des données dans deux comptes de stockage. Vous devez utiliser des disques durs distincts pour chaque compte de stockage et créer des tâches séparées par compte de stockage.

### <a name="prepare-your-drives"></a>Préparer vos lecteurs

La première étape lors de l’importation de données au moyen du service d’importation/exportation Azure consiste à préparer vos lecteurs à l’aide de l’outil de client Azure importer/exporter. Suivez les étapes ci-dessous pour préparer vos lecteurs.

1.  Identifier les données à importer. Il peut s’agir d’annuaires et fichiers autonomes sur le serveur local ou un partage réseau.  

2.  Déterminer le nombre de lecteurs que vous devrez selon la taille totale des données. Obtenir le nombre requis de lecteurs de disques durs 3,5 pouces SATA II/III.

3.  Identifier le compte de stockage cible, conteneur, répertoires virtuels et des objets BLOB.

4.  Déterminer les répertoires et/ou fichiers autonomes qui seront copiées dans chaque lecteur de disque dur.

5.  Utilisez l' [Outil d’importation/exportation Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) pour copier les données dans un ou plusieurs disques durs.

    - L’outil Azure importer/exporter crée des sessions de copie pour copier les données à partir de la source dans les lecteurs de disques durs. Dans une session de copie unique, l’outil peut copier un seul répertoire ainsi que ses sous-répertoires ou un seul fichier.

    - Vous devrez peut-être copier plusieurs sessions si votre source de données s’étend sur plusieurs répertoires.

    - Chaque lecteur de disque dur que vous préparez nécessite session au moins une copie.

6.  Vous pouvez spécifier la / chiffrer un paramètre pour activer le chiffrement Bitlocker sur le disque dur. Par ailleurs, vous pouvez également activer le chiffrement Bitlocker manuellement sur le disque dur et fournir la clé lors de l’exécution de l’outil.

7.  L’outil d’importation/exportation Azure génère un fichier de feuille lecteur pour chaque lecteur comme il est préparé. Le fichier journal de lecteur est stocké sur votre ordinateur local, pas sur le lecteur proprement dit. Vous allez télécharger le fichier journal lorsque vous créez la tâche d’importation. Un fichier journal de lecteur inclut l’ID de lecteur et la clé BitLocker, ainsi que d’autres informations sur le lecteur.
**Important**: chaque lecteur de disque dur vous préparez créent un fichier journal. Lorsque vous créez la tâche d’importation à l’aide du portail classique, vous devez télécharger tous les fichiers de journal des lecteurs qui font partie de cette tâche d’importation. Lecteurs sans le journal de fichiers ne seront pas traités.

8.  Ne modifiez pas les données sur les lecteurs de disque dur ou le fichier journal au terme de préparation du disque.

Vous trouverez ci-dessous les commandes et exemples pour préparer le disque dur à l’aide d’outil client Azure importer/exporter.

Azure importer/exporter client PrepImport commande outil pour la première session copier copier un répertoire :

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Exemple :**

Dans l’exemple ci-dessous, nous effectuez la copie tous les fichiers et sous-répertoires à partir de H:\Video sur le disque dur sur x. Les données importées dans le compte de stockage de destination spécifié par la clé de compte de stockage et dans le conteneur de stockage appelée vidéo. Si le conteneur de stockage ne figure pas, il sera créé. Cette commande également mettre en forme et chiffrer le lecteur de disque dur cible.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure importer/exporter client PrepImport commande outil pour les sessions ultérieures copier copier un répertoire :

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Pour les sessions de copie suivantes sur le disque dur même, spécifiez le même nom de fichier journal et fournir un ID nouvelle session ; Il est inutile pour fournir le stockage compte clé et le lecteur cible à nouveau, ni pour mettre en forme ou chiffrer le lecteur. Dans cet exemple nous sommes copie le dossier H:\Photo et ses sous-répertoires vers le même lecteur cible, dans le conteneur de stockage intitulé photo.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

L’outil de client Azure importer/exporter PrepImport commande pour la première session copier copier un fichier :

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

L’outil de client Azure importer/exporter PrepImport commande pour les sessions ultérieures copier copier un fichier :

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Mémoriser**: par défaut, les données importées sous forme d’objets BLOB bloc. Vous pouvez utiliser le paramètre /BlobType pour importer des données en tant qu’un objets BLOB de Page. Par exemple, si vous importez des fichiers de disque dur virtuel monter en tant que disques sur un ordinateur virtuel Azure, vous devez les importer en tant que Page BLOB. Si vous ne savez pas quel blob type à utiliser, vous pouvez spécifier /blobType:auto afin que nous puissions déterminer le type correct. Dans ce cas, tous les fichiers de disque dur virtuel et vhdx importées sous forme d’objets BLOB de Page et le reste importé sous forme d’objets BLOB bloc.

Afficher plus de détails à l’aide de l’outil de client Azure importer/exporter dans la [Préparation de disques durs pour l’importation](https://msdn.microsoft.com/library/dn529089.aspx).

En outre, reportez-vous à l' [Exemple de flux de travail pour préparer la disques durs pour une tâche d’importation](https://msdn.microsoft.com/library/dn529097.aspx) pour des instructions plus détaillées.  

### <a name="create-the-import-job"></a>Créer la tâche d’importation

1.  Une fois que vous avez préparé votre lecteur, accédez à votre compte de stockage sur le [portail classique](https://manage.windowsazure.com) et afficher le tableau de bord. Sous **Aperçu rapide**, cliquez sur **créer une tâche d’importation**. Passez en revue les étapes et activez la case à cocher pour indiquer que vous avez préparé votre lecteur et que vous avez le fichier de feuille de lecteur disponible.

2.  Étape 1, fournissent des informations de contact pour la personne responsable de cette tâche d’importation et d’une adresse d’expéditeur valide. Si vous souhaitez enregistrer les données du journal détaillée de la tâche d’importation, cochez l’option Enregistrer **le journal détaillé dans mon conteneur blob 'waimportexport'**.

3.  À l’étape 2, téléchargez les fichiers de feuille de lecteur que vous avez obtenu lors de l’étape de préparation du lecteur. Vous devrez télécharger un fichier pour chaque lecteur que vous avez préparé.

    ![Créer la tâche Importer - étape 3](./media/storage-import-export-service/import-job-03.png)

4.  Étape 3, entrez un nom descriptif pour la tâche d’importation. Notez que le nom que vous entrez peut contenir uniquement des lettres minuscules, des nombres, des traits d’union et des traits de soulignement, doit commencer par une lettre et ne peuvent pas contenir d’espaces. Vous allez utiliser le nom que vous choisissez pour effectuer le suivi de vos tâches lorsqu’ils sont en cours et une fois qu’elles sont terminées.

    Ensuite, sélectionnez votre région centre de données dans la liste. La zone Centre de données indique le centre de données et l’adresse vers lesquelles vous devez livrer votre package. Consultez le Forum aux questions ci-dessous pour plus d’informations.

5.  À l’étape 4, sélectionnez votre opérateur expéditeur dans la liste et entrez votre numéro de compte transporteur. Microsoft utilise ce compte pour envoyer les lecteurs vous une fois que votre tâche d’importation est terminée.

    Si vous avez votre numéro de suivi, sélectionnez votre opérateur de livraison dans la liste et entrez votre numéro de suivi.

    Si vous ne pas un numéro de référence n’avez encore, sélectionnez **je fournirai mes informations de livraison pour cette tâche d’importation une fois que j’ai expédiés mon package**, puis effectuez le processus d’importation.

6. Pour entrer votre numéro de suivi une fois que vous avez envoyé votre package, revenez à la page **Importer/exporter** pour votre compte de stockage sur le portail classique, sélectionnez votre travail dans la liste, puis sélectionnez les **Informations de livraison**. Naviguer dans l’Assistant et entrez votre numéro de suivi à l’étape 2.

    Si le numéro de suivi n’est pas mis à jour au sein de 2 semaines de création de la tâche, le travail va expirer.

    Si la tâche se trouve dans l’état de création, de livraison ou de transfert, vous pouvez également mettre à jour votre numéro de compte transporteur à l’étape 2 de l’Assistant. Une fois que la tâche est dans l’état d’emballage, vous ne pouvez pas mettre à jour votre numéro de compte transporteur pour cette tâche.

7. Vous pouvez suivre la progression du travail dans le tableau de bord du portail. Voir la signification de chaque état de la tâche dans la section précédente en [affichant votre statut de la tâche](#viewing-your-job-status).

## <a name="how-to-create-an-export-job"></a>Comment créer une tâche d’exportation ?

Créer une tâche d’exportation pour indiquer le service d’importation/exportation que vous allez proposer un ou plusieurs lecteurs vides au centre de données afin que les données peuvent être exportées à partir de votre compte de stockage pour les lecteurs et les lecteurs puis avez achetées.

### <a name="prepare-your-drives"></a>Préparer vos lecteurs

Vérifications préalables suivantes sont recommandées pour préparer vos lecteurs pour une tâche d’exportation :

1. Vérifier le nombre de disques nécessaires à l’aide de la commande PreviewExport de l’outil Azure importer/exporter. Pour plus d’informations, voir [L’aperçu l’utilisation de lecteur pour une tâche d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Il vous permet d’afficher un aperçu de l’utilisation du lecteur pour les objets BLOB que vous avez sélectionné, en fonction de la taille des lecteurs vous vous apprêtez à utiliser.

2. Vérifiez que vous pouvez en lecture/écriture sur le disque dur qui est expédié pour la tâche d’exportation.

### <a name="create-the-export-job"></a>Créer la tâche d’exportation

1.  Pour créer une tâche d’exportation, accédez à votre compte de stockage dans le [portail classique](https://manage.windowsazure.com), puis afficher le tableau de bord. Sous **Aperçu rapide**, cliquez sur **créer une tâche d’exportation** et suivez les instructions de l’Assistant.

2.  À l’étape 2, fournissent des informations de contact pour la personne responsable de cette tâche d’exportation. Si vous souhaitez enregistrer les données du journal détaillée de la tâche d’exportation, cochez l’option Enregistrer **le journal détaillé dans mon conteneur blob 'waimportexport'**.

3.  Étape 3, spécifiez les données blob que vous souhaitez exporter à partir de votre compte de stockage pour votre vide ou les lecteurs. Vous pouvez choisir d’exporter toutes les données blob du compte de stockage, ou vous pouvez spécifier qui BLOB ou ensembles d’objets BLOB à exporter.

    Pour spécifier un objet blob à exporter, utilisez le sélecteur **d’Égal à** et spécifiez le chemin d’accès relatif au blob, en commençant par le nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.

    Pour spécifier tous les objets BLOB commençant par un préfixe, utilisez le sélecteur **Commence par** et spécifiez le préfixe, en commençant par une barre oblique « / ». Le préfixe peut être le préfixe le nom du conteneur, le nom du conteneur complète ou le nom du conteneur complète suivi par le préfixe du nom du blob.

    Le tableau suivant répertorie des exemples de chemins d’accès blob valides :

    Sélecteur de|Chemin d’accès des objets BLOB|Description
    ---|---|---
    Commence par|/|Exporte tous les objets BLOB dans le compte de stockage
    Commence par|/$root /|Exporte tous les objets BLOB dans le conteneur racine
    Commence par|/Book|Exporte tous les objets BLOB dans n’importe quel conteneur qui commence par le préfixe **livre**
    Commence par|/Music/|Exporte tous les objets BLOB dans le conteneur **musique**
    Commence par|/ musique/amour|Exporte tous les objets BLOB dans le conteneur **musique** qui commencent par le préfixe **appréciez**
    Égal à|$root/logo.bmp|Exportations blob **logo.bmp** dans le conteneur racine
    Égal à|videos/Story.mp4|Exportations blob **story.mp4** dans conteneur **vidéos**

    Vous devez fournir les chemins d’accès blob dans les formats valides pour éviter les erreurs pendant le traitement, comme illustré dans cette capture d’écran.

    ![Créer tâche d’exportation - étape 3](./media/storage-import-export-service/export-job-03.png)


4.  À l’étape 4, entrez un nom descriptif pour la tâche d’exportation. Le nom que vous entrez peut contenir uniquement des lettres minuscules, des nombres, des traits d’union, et des traits de soulignement, doit commencer par une lettre et ne peut pas contenir d’espaces.

    La zone Centre de données indique le centre de données à laquelle vous devrez livrer votre package. Consultez le Forum aux questions ci-dessous pour plus d’informations.

5.  À l’étape 5, sélectionnez votre opérateur expéditeur dans la liste et entrez votre numéro de compte transporteur. Microsoft utilise ce compte pour envoyer vos lecteurs vous une fois que votre tâche d’exportation est terminée.

    Si vous avez votre numéro de suivi, sélectionnez votre opérateur de livraison dans la liste et entrez votre numéro de suivi.

    Si vous ne pas un numéro de référence n’avez encore, sélectionnez **je fournirai mes informations d’expédition pour cette tâche exporter une fois que j’ai expédiés mon package**, puis effectuez le processus d’exportation.

6. Pour entrer votre numéro de suivi une fois que vous avez envoyé votre package, revenez à la page **Importer/exporter** pour votre compte de stockage sur le portail classique, sélectionnez votre travail dans la liste, puis sélectionnez les **Informations de livraison**. Naviguer dans l’Assistant et entrez votre numéro de suivi à l’étape 2.

    Si le numéro de suivi n’est pas mis à jour au sein de 2 semaines de création de la tâche, le travail va expirer.

    Si la tâche se trouve dans l’état de création, de livraison ou de transfert, vous pouvez également mettre à jour votre numéro de compte transporteur à l’étape 2 de l’Assistant. Une fois que la tâche est dans l’état d’emballage, vous ne pouvez pas mettre à jour votre numéro de compte transporteur pour cette tâche.

    > [AZURE.NOTE] Si le blob à exporter est en cours d’utilisation au moment de la copie sur disque dur, service Azure importer/exporter prendre un instantané du blob et copier la capture instantanée.

7.  Vous pouvez suivre la progression du travail dans le tableau de bord dans le portail classique. Voir la signification de chaque état de la tâche dans la section précédente sur « Afficher votre statut de la tâche ».

8.  Une fois que vous recevez les lecteurs avec vos données exportées, vous pouvez afficher et copier les clés BitLocker générés par le service correspondant à votre lecteur. Accédez à votre compte de stockage dans le portail classique, puis cliquez sur l’onglet Importer/exporter. Sélectionnez votre tâche d’exportation de la liste, puis cliquez sur le bouton Afficher les clés. Les touches BitLocker s’affichent comme indiqué ci-dessous :

    ![Afficher les clés BitLocker pour la tâche d’exportation](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Passez en revue la section Forum aux questions ci-dessous car elle traite de questions les plus fréquentes rencontrés lors de l’utilisation de ce service.

## <a name="frequently-asked-questions"></a>Forum aux Questions ##


**Combien de temps faut-il pour copier mes données après que mon lecteurs atteint le centre de données ?**

Le temps pour copier varie selon différents facteurs comme type de tâche, le type et la taille des données copiées, taille des disques fourni et existant la charge de travail. Il peut varier de quelques jours à quelques semaines, en fonction de ces facteurs. Par conséquent, il est difficile de fournir une estimation générale. Le service tente d’optimiser votre travail en copiant plusieurs lecteurs en parallèle lorsque cela est possible. Si vous avez une tâche critique importation/exportation de temps, communiquer aux nous pour une estimation.

**Quand dois-je utiliser le Service d’importation/exportation Azure ?**
Une doit prendre en compte à l’aide de Azure importer exporter si chargement ou le téléchargement réseau prend à peu près comme estimation plus de 7 jours. Vous pouvez calculer combien de temps il faudra à l’aide de n’importe quel calculateur en ligne ou en le [téléchargeant](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) celui situé dans notre Azure importer exporter reste l’exemple d’API dans référentiel exemples Azure à [Calculatrice de vitesse de transférer des données](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). Ce n’est pas un calcul exact, mais uniquement une indication approximative.

**Puis-je utiliser le service Azure importer/exporter avec un compte de stockage du Gestionnaire de ressources ?**

Non, vous ne pouvez pas copier des données vers ou à partir d’un compte de stockage Gestionnaire de ressources directement au moyen du service Azure importer/exporter. Le service prend uniquement en charge les comptes de stockage classique. Prise en charge le Gestionnaire de ressources stockage compte sera bientôt. Comme alternative, vous pouvez importer des données dans un compte de stockage classique et effectuer une migration vers votre compte de stockage Gestionnaire de ressources à l’aide de [AzCopy](storage-use-azcopy.md) ou CopyBlob.

**Puis-je copier fichiers Azure au moyen du service d’importation/exportation Azure ?**

Non, le service d’importation/exportation Azure prend uniquement en charge des objets BLOB de Page et les objets BLOB bloc. Tous les autres types de stockage, y compris les fichiers Azure, des tableaux et des files d’attente ne sont pas pris en charge.

**Le service d’importation/exportation Azure n’est disponible pour les abonnements fournisseur ?**

Non, le service Azure importer/exporter ne prend pas en charge abonnements fournisseur. La prise en charge est ajouté à l’avenir.

**Puis-je ignorer l’étape de préparation d’un lecteur d’une importation ou puis-je préparer un lecteur sans la copier ?**

N’importe quel lecteur que vous souhaitez envoyer pour l’importation de données doit être préparé à l’aide de l’outil de client Azure importer/exporter. Vous devez utiliser l’outil client pour copier les données sur le disque.

**Ai-je besoin effectuer une préparation disque lors de la création d’une tâche d’exportation ?**

Aucune, mais certaines vérifications préalables sont recommandées. Vérifier le nombre de disques nécessaires à l’aide de la commande PreviewExport de l’outil Azure importer/exporter. Pour plus d’informations, voir [L’aperçu l’utilisation de lecteur pour une tâche d’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx). Il vous permet d’afficher un aperçu de l’utilisation du lecteur pour les objets BLOB que vous avez sélectionné, en fonction de la taille des lecteurs vous vous apprêtez à utiliser. Vérifiez également que vous pouvez lire et écrire sur le disque dur qui est expédié pour la tâche d’exportation.

**Que se passe-t-il si j’envoie accidentellement un disque dur qui n’est pas conforme aux exigences pris en charge ?**

Le centre de données Azure renvoie le lecteur n’est pas conforme à la configuration requise pris en charge pour vous. Si seulement certains lecteurs dans le package requise pour la prise en charge, ces lecteurs seront traités, et les lecteurs qui ne répondent pas vous être renvoyés.

**Puis-je annuler mon travail ?**

Vous pouvez annuler une tâche lorsque son statut est création ou de livraison.

**Combien de temps puis-je afficher l’état de tâches terminées portail classique ?**

Vous pouvez afficher l’état de tâches terminées pour 90 jours au maximum. Tâches terminées sont supprimés après 90 jours.

**Si vous voulez importer ou exporter des lecteurs de plus de 10, que dois-je faire ?**

Une importation ou exportation travail faire référence à seulement 10 lecteurs dans une seule tâche pour le service d’importation/exportation. Si vous voulez envoyer plus de 10 disques, vous pouvez créer plusieurs tâches. Lecteurs qui sont associées à la même tâche doivent être fournis ensemble dans le même package.

**Puis-je utiliser une carte USB SATA qui n’est pas dans la liste recommandée ?**

Si vous avez un convertisseur qui n’est pas répertorié ci-dessus, vous pouvez essayer d’exécuter l’outil d’importation/exportation Azure à l’aide de votre convertisseur pour préparer le disque et voyez si cela fonctionne avant d’acheter un convertisseur pris en charge.

**Le service met en forme les lecteurs avant de les retourner ?**

Non. Tous les lecteurs sont chiffrés avec BitLocker.

**Puis-je acheter lecteurs pour les tâches d’importation/exportation de Microsoft ?**

Non. Vous devrez livrer vos propres lecteurs pour les deux importer et exporter des tâches.

**Une fois la tâche d’importation terminée, que mes données ressemblera dans le compte de stockage ? Est préservé mon arborescence ?**

Lorsque vous préparez un disque dur pour une tâche d’importation, la destination est spécifiée par la /dstdir : paramètre. Il s’agit du conteneur de destination dans le compte de stockage dans lequel les données à partir du disque dur sont copiées. Partie de ce conteneur de destination, répertoires virtuels sont créés pour les dossiers du disque dur et des objets BLOB sont créés pour les fichiers.

**Si le lecteur comporte des fichiers qui existent déjà dans mon compte de stockage, le service de remplacer des objets BLOB existants dans mon compte de stockage ?**

Lors de la préparation du lecteur, vous pouvez spécifier si les fichiers de destination doivent être remplacés ou ignorées en utilisant le paramètre appelé /Disposition : < renommer | remplacer aucun | remplacer >. Par défaut, le service sera renommer les nouveaux fichiers plutôt que de remplacer des objets BLOB existants.

**L’outil de client Azure importer/exporter est compatible avec les systèmes d’exploitation 32 bits ?**
Non. L’outil client n’est pas compatible avec les systèmes d’exploitation 64 bits Windows. Reportez-vous à la section systèmes d’exploitation dans les [conditions préalables](#pre-requisites) pour obtenir une liste complète des versions du système d’exploitation pris en charge.

**Dois-je inclure rien d’autre que le disque dur dans mon package ?**

Veuillez envoyer uniquement vos disques durs. N’incluez pas les éléments, tels que les câbles d’alimentation ou câbles USB.

**Je dois livrer mes lecteurs à l’aide de FedEx ou DHL ?**

Vous pouvez livrer lecteurs au centre de données à l’aide de n’importe quel opérateur connu comme FedEx, DHL, alimentation de secours ou US Postal Service. Toutefois, pour les lecteurs vous à partir du centre de données d’expédition, vous devez fournir un numéro de compte FedEx aux États-Unis et l’Union européenne, ou un numéro de compte dans les régions Asie et Australie DHL.

**Existe-t-il des restrictions avec livraison mon lecteur à l’étranger ?**

Veuillez noter que le média physique que vous fournissez doivent croisées bordures internationales. Vous êtes chargé de s’assurer que vos supports physiques et des données sont importées et/ou exportées en respectant la législation applicable. Avant le média physique d’expédition, vérifiez auprès de vos conseillers pour vérifier que vos données et éléments multimédias peuvent légalement être expédiées au centre de données identifié. Cela vous aidera à vérifier qu’il connecte à Microsoft rapidement.

**Lorsque vous créez une tâche, l’adresse de livraison est un emplacement qui est la différent entre mon emplacement de compte de stockage. Que dois-je faire ?**

Certains des emplacements de stockage compte sont mappés aux emplacements d’expédition secondaire. Précédemment lieux d’expédition disponibles peuvent également être temporairement mappées à d’autres emplacements. Toujours vérifier l’adresse de livraison fournie lors de la création de travail avant d’expédition de vos lecteurs.

**Pourquoi mon statut de travail dans le classique portail dites « livraison » lorsque vous avez trouvé le site Web Carrier mon package est remis ?**

L’état dans le portail classique change de livraison à transfert lorsque le lecteur de démarrage du traitement. Si votre lecteur a atteint l’installation, mais n’a pas commencé traitement, votre statut de la tâche s’affichent en tant qu’envoi.

**Lors de l’expédition mon lecteur, le transporteur vous invite à indiquer le nom de contact de centre de données et le numéro de téléphone. Ce que transmettre ?**

Le numéro de téléphone est fourni pour vous lors de la création de la tâche. Si vous avez besoin d’un nom de contact, contactez-nous à waimportexport@microsoft.com et nous vous fournira ces informations.

**Puis-je utiliser le service Azure importer/exporter pour copier des boîtes aux lettres PST et les données de SharePoint vers Office 365 ?**

Reportez-vous à [des fichiers PST importer ou données SharePoint vers Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Puis-je utiliser le service Azure importer/exporter pour copier mes sauvegardes en mode hors connexion dans le Service de sauvegarde Azure ?**

Reportez-vous au [flux de travail de sauvegarde en mode hors connexion dans Azure sauvegarde](../backup/backup-azure-backup-import-export.md).

## <a name="see-also"></a>Voir aussi :

- [La configuration de l’outil de client Azure importer/exporter](https://msdn.microsoft.com/library/dn529112.aspx)

- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

- [Exemple d’API reste Azure importer exporter](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
