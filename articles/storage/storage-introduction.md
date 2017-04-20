<properties
    pageTitle="Introduction au stockage | Microsoft Azure"
    description="Vue d’ensemble d’Azure, le stockage de Microsoft en ligne des données dans le cloud. Découvrez comment utiliser la meilleure solution de stockage cloud disponibles dans vos applications."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introduction au stockage de Microsoft Azure

## <a name="overview"></a>Vue d’ensemble

Stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur durabilité, la disponibilité et extensibilité élevées en fonction des besoins de leurs clients. En lisant cet article, les développeurs, informatique et décision commerciale décideurs pouvant en savoir plus sur :

- Qu’est le stockage Azure, et comment vous pouvez tirer parti de celle-ci dans le cloud, mobile, serveur et applications de bureau
- Quels types de données que vous pouvez stocker avec les services de stockage Azure : blob de données (objet), les données de la table NoSQL, messages file d’attente et les partages de fichiers.
- Comment accéder à vos données dans le stockage Azure est géré
- Comment vos données de stockage Azure sont rendues résistants via redondance et de réplication
- Que faire ensuite pour créer votre première application Azure Storage

Pour être rapidement opérationnel avec le stockage Azure, voir [prise en main le stockage Azure dans cinq minutes](storage-getting-started-guide.md).

Pour plus d’informations sur les outils, bibliothèques et autres ressources pour l’utilisation de stockage Azure, voir les [Étapes](#next-steps) ci-dessous.

## <a name="what-is-azure-storage"></a>Quel est le stockage Azure ?

Le cloud computing permet aux nouveaux scénarios pour les applications nécessitant un stockage scalable, résistant et disponibilité de leurs données – qui est exactement pourquoi Microsoft développé stockage Azure. En plus de ce qui permet aux développeurs de créer des applications à grande échelle pour prendre en charge de nouveaux scénarios, le stockage Azure fournit également la base du stockage Machines virtuelles pour Azure, une preuve supplémentaire sa fiabilité.

Stockage Azure étant considérablement format SVG, vous pouvez stocker et processus des centaines de To de données pour prendre en charge les scénarios de données Long requis par analyse scientifique, financière et les applications multimédia. Ou vous pouvez stocker les petites quantités de données requises pour un site Web de petite entreprise. À l’endroit où se trouvent de vos besoins, vous payez uniquement pour les données que sont stockés. Stockage Azure actuellement stocke les dizaines de codage d’objets customer uniques et gère des millions de requêtes par seconde en moyenne.

Stockage Azure étant élastique, vous pouvez concevoir des applications pour un large public global et évoluer ces mêmes applications selon vos besoins - en termes de la quantité de données stockées et le nombre de requêtes effectuées par rapport à celui-ci. Vous payez uniquement pour ce que vous utilisez, et uniquement lorsque vous l’utilisez.

Stockage Azure utilise un système partition automatique qui répartit automatiquement la charge vos données basées sur le trafic. Cela signifie que, en tant que les demandes de développement de votre application, le stockage Azure affecte automatiquement les ressources appropriées pour y répondre.

Stockage Azure est accessible à partir de n’importe où dans le monde, à partir de n’importe quel type d’application, s’il s’exécute dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile ou périphérique tablette. Vous pouvez utiliser le stockage Azure dans des scénarios mobiles dans lequel l’application stocke un sous-ensemble de données sur l’appareil et synchronise avec un ensemble complet des données stockées dans le cloud.

Stockage Azure prend en charge les clients à l’aide d’un ensemble centralisée de systèmes d’exploitation (y compris Windows et Linux) et une variété de langages de programmation (y compris .NET, Java, Node.js, Python, Ruby, PHP et C++ et langages de programmation mobiles) pour le développement pratique. Stockage Azure expose également des ressources de données via des API REST simples, qui sont disponibles pour n’importe quel client capable d’envoyer et recevoir des données via HTTP/HTTPS.

Le stockage Premium Azure offre prise en charge de disque hautes performances et à faible latence des e/s intensives les charges de travail en cours d’exécution sur le Machines virtuelles Azure. Grâce au stockage Azure Premium, vous pouvez joindre plusieurs disques de données permanente à une machine virtuelle et les configurer pour répondre à vos besoins de performances. Chaque disque de données est sauvegardée par un disque SSD dans le stockage Azure Premium pour optimiser les performances d’e/s. Voir [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](storage-premium-storage.md) pour plus d’informations.

## <a name="introducing-the-azure-storage-services"></a>Présentation des Services de stockage Azure

Stockage Azure fournit les quatre services suivants : Blob storage, stockage de tables, stockage file d’attente et le stockage de fichiers.

- Stockage BLOB stocke les données de l’objet non structurées. Un blob peut être n’importe quel type de texte ou des données binaires, tel qu’un document, un fichier multimédia ou un programme d’installation d’application. Stockage d’objets BLOB est également appelée stockage d’objets.
- Stockage de tables stocke les jeux de données structurées. Stockage de table est un magasin de données de l’attribut key NoSQL, qui permet un développement rapide et accéder rapidement de grandes quantités de données.
- Stockage de file d’attente fournit une messagerie fiable pour le traitement des flux de travail et pour les communications entre les composants des services en nuage.
- Stockage de fichiers offre un stockage partagé pour les applications héritées en utilisant le protocole PME standard. Azure machines virtuelles et des services cloud peuvent partager des données de fichier entre composants d’application via partages montés et en local applications peuvent accéder aux données du fichier dans un partage via le service de fichiers API REST.

Un compte de stockage Azure est un compte sécurisé qui permet d’accéder aux services dans le stockage Azure. Votre compte de stockage fournit l’espace de noms unique pour vos ressources de stockage. L’image ci-dessous montre les relations entre les ressources de stockage Azure dans un compte de stockage :

![Ressources de stockage Azure](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Stockage d’objets BLOB

Pour les utilisateurs de grandes quantités de données de l’objet non structurées à stocker dans le nuage, stockage d’objets Blob offre une solution rentable et format SVG. Vous pouvez utiliser le stockage Blob pour stocker le contenu tel que :

- Documents
- Données sociales tels que des photos, vidéos, musique et des blogs
- Effectuer des sauvegardes régulières des fichiers, des ordinateurs, des bases de données et des appareils
- Images et du texte pour les applications web
- Données de configuration des applications en nuage
- Données volumineuses, telles que les journaux et les autres jeux de données volumineux

Chaque blob est organisée dans un conteneur. Conteneurs offrent également un moyen utile pour affecter des stratégies de sécurité à des groupes d’objets. Un compte de stockage peut contenir un nombre quelconque de conteneurs et un conteneur peut contenir un nombre quelconque d’objets BLOB, jusqu'à la limite de capacité de 500 To du compte de stockage.  

BLOB storage propose trois types d’objets BLOB, bloquer des objets BLOB, ajoutez des objets BLOB et des objets BLOB de page (disques).

- Objets BLOB bloc optimisées en continu et le stockage d’objets cloud et sont un bon choix pour le stockage des documents, des fichiers multimédias, des sauvegardes etc..
- Ajouter des objets BLOB sont similaires aux objets BLOB bloc, mais optimisées pour les opérations d’ajout. Un blob Ajout pouvant être mis à jour uniquement en ajoutant un nouveau bloc à la fin. Ajouter des objets BLOB sont un bon choix pour les scénarios telles que l’enregistrement, où les nouvelles données doivent être écrites uniquement à la fin de l’objet blob.
- Page des objets BLOB sont optimisés pour représentant IaaS disques et prenant en charge aléatoire écrit et peut être jusqu'à 1 To. Un réseau machine virtuelle Azure associés IaaS disque est un disque dur virtuel stocké en tant qu’un blob de page.

Pour les jeux de données volumineux où les contraintes de réseau apporter chargement ou le téléchargement des données vers le stockage Blob via le câble irréaliste, vous pouvez livrer un disque dur à Microsoft pour importer ou exporter des données directement à partir du centre de données. Voir [utiliser le Service d’importation/exportation Microsoft Azure pour transférer des données pour le stockage Blob](storage-import-export-service.md).

## <a name="table-storage"></a>Stockage de table

Applications modernes exigent souvent banques de données avec une flexibilité supérieure et la flexibilité à générations précédentes de logiciels requis. Stockage de tables offre un stockage hautement disponible et considérablement scalable, afin que votre application peut s’adapter automatiquement pour répondre à la demande de l’utilisateur. Stockage de table est stockage de clé/attribut NoSQL de Microsoft : il a une conception schemaless, ce qui diffère de bases de données relationnelles traditionnels. Avec un magasin de données schemaless, il est facile d’adapter vos données selon les besoins de votre evolve application. Stockage de table est facile à utiliser, afin que les développeurs peuvent créer rapidement des applications. Accès aux données est rapide et rentable pour toutes sortes d’applications.  Stockage de table est généralement sensiblement inférieur dans coût traditionnel SQL pour les volumes de données similaires.

Stockage de table est un magasin attribut key, ce qui signifie que chaque valeur dans une table est stocké avec un nom de la propriété tapé. Le nom de la propriété peut être utilisé pour filtrer et définir des critères de sélection. Un ensemble de propriétés et leurs valeurs comprennent une entité. Stockage de Table étant schemaless, deux entités dans la même table peuvent contenir différents ensembles de propriétés, et ces propriétés peuvent être de différents types.

Vous pouvez utiliser le stockage de Table pour stocker les jeux de données flexibles, telles que des données utilisateur pour les applications web, des carnets d’adresses, informations sur les périphériques et tout autre type de métadonnées nécessaires à votre service.  Vous pouvez enregistrer un nombre quelconque d’entités dans un tableau, et un compte de stockage peut contenir un nombre quelconque de tables, jusqu'à la capacité maximale du compte de stockage.

Comme des objets BLOB et les files d’attente, les développeurs peuvent gérer et Table access protocoles de stockage à l’aide de reste standard, mais le stockage de tables prennent également en charge un sous-ensemble du protocole OData, simplification des capacités de requêtes avancées et en activant JSON et AtomPub (basé sur XML) formats.

Pour les applications basées sur Internet aujourd'hui, bases de données NoSQL comme stockage de Table offrent une alternative populaire bases de données relationnelles traditionnel.

## <a name="queue-storage"></a>Stockage de file d’attente

Dans la conception d’applications pour échelle, les composants d’application sont souvent découplées, afin qu’ils peuvent faire évoluer indépendamment. Stockage de file d’attente fournit une solution de messagerie fiable pour la communication asynchrone entre les composants d’application, si elles s’exécutent dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Stockage de file d’attente prend également en charge la gestion des tâches asynchrones et la création de processus de flux de travail.

Un compte de stockage peut contenir un nombre quelconque de files d’attente. Une file d’attente peut contenir un ou plusieurs messages, jusqu'à la capacité maximale du compte de stockage. Des messages individuels peuvent être jusqu'à 64 Ko.

## <a name="file-storage"></a>Stockage de fichiers

Stockage de fichiers Azure offre partages de fichiers PME sur le nuage, afin que vous pouvez migrer des applications existantes qui s’appuient sur les partages de fichiers vers Azure rapidement et sans réécritures coûteuses. Avec le stockage de fichiers de Azure, applications qui s’exécutent dans des machines virtuelles Azure ou les services en nuage peuvent monter un partage de fichiers dans le cloud, comme une application de bureau montages un partage de PME classique. N’importe quel nombre des composants d’application peut ensuite monter et accéder au partage de stockage de fichiers simultanément.

Dans la mesure où un partage de stockage de fichiers est un partage de fichiers PME standard, applications qui s’exécutent dans Azure peuvent accéder aux données dans le partage via le fichier système I/O APIs. Les développeurs peuvent donc exploiter leur code existant et les compétences requises pour migrer les applications existantes. Informatique pouvez utiliser les applets de commande PowerShell pour créer, monter et gérer des partages de stockage de fichiers dans le cadre de l’administration des applications Azure.

Comme les autres services de stockage Azure, le stockage de fichiers expose une API REST pour accéder aux données dans un partage. Applications sur site peuvent appeler le stockage de fichiers API REST pour accéder aux données dans un partage de fichiers. De cette façon, une entreprise peut choisir de migrer certaines applications héritées vers Azure et continuer à d’autres personnes d’exécuter au sein de leur organisation. Notez qu’un partage de fichiers de montage est uniquement possible pour les applications en cours d’exécution dans Azure ; le partage de fichiers via l’API REST peut uniquement accéder à une application locale.

Applications distribuées permet également le stockage de fichiers pour stocker et partager des données d’application utiles et développement et outils de test. Par exemple, une application peut stocker des fichiers de configuration et exporte les données de diagnostic tels que les journaux et les mesures de blocage dans un fichier de stockage partager afin qu’ils soient disponibles à plusieurs machines virtuelles ou rôles. Les développeurs et les administrateurs peuvent stocker utilitaires dont ils ont besoin pour créer ou gérer une application dans un partage de stockage de fichiers qui n’est disponible pour tous les composants, plutôt que de les installer sur chaque ordinateur virtuel ou instance de rôle.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Accès aux objets Blob, Table, file d’attente et des ressources de fichiers

Par défaut, seul le propriétaire du compte de stockage peut accéder aux ressources du compte de stockage. Pour la sécurité de vos données, toutes les requêtes adressées par rapport aux ressources dans votre compte doivent être authentifié. Authentification repose sur un modèle de clé partagée. Objets BLOB peuvent également être configurés pour prendre en charge l’authentification anonyme.

Votre compte de stockage est affectée deux privé touches d’accès rapide sur la création de qui sont utilisés pour l’authentification. Deux clés garantit que votre application reste disponible lorsque vous régénérez régulièrement les touches comme pratique de gestion de clés de sécurité courantes.

Si vous avez besoin pour permettre aux utilisateurs sous contrôle de l’accès à vos ressources de stockage, puis vous pouvez créer une signature accès partagé. Une signature accès partagé (sa) est un jeton qui peut être ajouté à une URL qui autorise l’accès délégué à une ressource de stockage. Toute personne qui possède le jeton peut accéder à la ressource qu’il pointe vers avec les autorisations qu'il indique, pour la période de temps qu’il est valide. Depuis la version 04/2015 / 05, le stockage Azure prend en charge les deux types de signatures accès partagé : associations de sécurité de service et associations de sécurité du compte.

Le service de sa délégués accès à une ressource dans un seul de ces services de stockage : le service Blob, file d’attente, un tableau ou fichier.

Un compte sa délégués accès aux ressources dans un ou plusieurs des services de stockage. Vous pouvez déléguer l’accès à des opérations de niveau de service qui ne sont pas disponibles avec un service associations de sécurité. Vous pouvez également accès délégué à lire, écrire et supprimer des opérations sur conteneurs blob, des tableaux, des files d’attente et des partages de fichiers qui ne sont pas autorisées avec un service associations de sécurité.

Enfin, vous pouvez spécifier qu’un conteneur et ses objets BLOB ou un blob spécifique, sont disponibles pour l’accès public. Lorsque vous indiquez qu’un conteneur ou blob est public, tout le monde peut le lire de manière anonyme ; Aucune authentification n’est requise.  Publics conteneurs et des objets BLOB sont utiles pour exposer des ressources comme des supports et les documents qui sont hébergés sur des sites Web.  Pour réduire la latence du réseau pour une audience globale, vous pouvez mettre en cache de données blob utilisé par des sites Web avec le CDN Azure.

Pour plus d’informations sur les signatures accès partagé, reportez-vous [à l’aide de l’accès des Signatures (sa partagées)](storage-dotnet-shared-access-signature-part-1.md) . Pour plus d’informations sur un accès sécurisé à votre compte de stockage, voir [Gérer les accès en lecture anonymes à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md) et [l’authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) .

## <a name="replication-for-durability-and-high-availability"></a>Réplication de durabilité et de disponibilité

Les données dans votre compte de stockage de Microsoft Azure sont toujours répliquées pour vous assurer que durabilité et la disponibilité. La réplication copie vos données, au sein du même centre de données ou à un deuxième centre de données, en fonction de quelle option de réplication que vous choisissez. Réplication protéger vos données et conserve votre application le temps en cas de panne du matériel transitoires. Si vos données sont répliquées sur un deuxième centre de données, également pour la protection de vos données contre une panne dans l’emplacement principal.

La réplication garantit que votre compte de stockage répond au [Niveau de Service du contrat de stockage](https://azure.microsoft.com/support/legal/sla/storage/) quoi qu’il arrive échecs. Consultez que le contrat SLA pour plus d’informations sur le stockage Azure garanties de durabilité et de disponibilité. 

Lorsque vous créez un compte de stockage, vous pouvez sélectionner une des options de réplication suivantes :  

- **Stockage localement redondante (LRS).** Stockage localement redondant gère trois copies de vos données. LRS est répliquée trois fois au sein d’un centre de données unique dans une seule région. LRS protège vos données contre les défaillances matérielles normal, mais pas à partir de l’échec d’un centre de données unique.  

    LRS est proposé avec une remise. Pour durer maximale, nous vous recommandons d’utiliser stockage geo redondantes, décrit ci-dessous.


- **Stockage zone redondantes (ZRS).** Stockage zone redondants gère trois copies de vos données. ZRS est répliquées trois fois sur deux ou trois installations, dans une région unique ou entre deux régions, fournir une durabilité élevée que LRS. ZRS garantit que vos données sont résistants dans une région unique.  

    ZRS fournit un niveau supérieur de durabilité à LRS ; Toutefois, pour durer maximale, nous vous recommandons d’utiliser stockage geo redondantes, décrit ci-dessous.  

    > [AZURE.NOTE] ZRS est actuellement disponible uniquement pour les objets BLOB bloc et est uniquement prise en charge pour les versions 2014-02-14 et versions ultérieures.
    >
    > Une fois que vous avez créé votre compte de stockage et sélectionné ZRS, vous ne pouvez pas convertir à utiliser pour tout autre type de réplication, ou vice versa.

- **Stockage Geo redondantes (GRS)**. GRS conserve six copies de vos données. Avec GRS, vos données sont répliquées trois fois dans la zone principale et sont également répliquées trois fois dans une zone secondaire des centaines de miles en dehors de la région principale, assurant le niveau le plus élevé de durabilité. En cas de panne la région principale, le stockage Azure ne basculement à la zone secondaire. GRS garantit que vos données sont résistants dans deux régions distinctes.

    Pour plus d’informations sur les paires de principal et secondaire par région, voir [Régions Azure](https://azure.microsoft.com/regions/).

- **Stockage geo redondants accès en lecture (RA GRS)**. Stockage geo redondants accès en lecture réplique vos données dans un emplacement géographique secondaire et fournit également un accès en lecture à vos données dans l’emplacement secondaire. Accès en lecture geo redondants stockage vous permet d’accéder à vos données depuis le serveur principal ou l’emplacement secondaire, au cas où un seul emplacement n’est plus disponible. Stockage geo redondants accès en lecture est l’option par défaut pour votre compte de stockage par défaut lors de sa création. 

    > [AZURE.IMPORTANT] Vous pouvez modifier la manière dont vos données sont répliquées après la création de votre compte de stockage, sauf si vous spécifiez ZRS lorsque vous avez créé le compte. Toutefois, notez que vous risquez de subir un transfert de données unique supplémentaires si vous aviez souscrit LRS GRS ou RA GRS de coût.

Pour plus d’informations sur les options de réplication de stockage, voir [la réplication du stockage Azure](storage-redundancy.md) .

Informations de tarification pour la réplication de compte de stockage, voir [Tarifs de stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur les services sont disponibles dans chaque région, consultez [Azure régions](https://azure.microsoft.com/regions/#services) .

Pour savoir architecturale durabilité avec stockage Azure, consultez [SOSP papier - stockage Azure : A hautement disponible Cloud Service de stockage grâce à la cohérence fort](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Transférer des données vers et depuis le stockage Azure

Vous pouvez utiliser l’utilitaire AzCopy pour copier blob, fichier et des données d’un tableau dans votre compte de stockage ou plusieurs comptes de stockage. Pour plus d’informations, voir [transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) .

AzCopy intégré en haut de la [Bibliothèque de déplacement de données Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), qui est actuellement disponible dans l’aperçu.

Le service d’importation/exportation Azure permet d’importer des données blob ou exporter des données blob à partir de votre compte de stockage via un disque dur envoyé dans le centre de données Azure. Pour plus d’informations sur le service d’importation/exportation, voir [utiliser le Service Microsoft Azure importer/exporter à transférer des données pour le stockage d’objets Blob](storage-import-export-service.md).

## <a name="pricing"></a>Tarifs

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>Outils, des bibliothèques et des API de stockage

Ressources de stockage Azure sont accessibles par n’importe quelle langue qui peut envoyer des demandes HTTP/HTTPS. En outre, le stockage Azure fournit des bibliothèques de programmation pour plusieurs langues courantes. Ces bibliothèques simplifient de nombreux aspects de l’utilisation de stockage Azure par la gestion des informations utiles telles que synchrone et asynchrone de l’appel, le traitement par lots d’opérations, gestion des exceptions, nouvelles tentatives automatiques, comportement opérationnel et ainsi de suite. Les bibliothèques sont actuellement disponibles pour les langues et plateformes, avec d’autres personnes dans le pipeline suivants :

### <a name="azure-storage-data-services"></a>Services de données de stockage Azure

- [Services de stockage API REST](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Bibliothèque de Client de stockage pour .NET, Windows Phone et Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Bibliothèque de Client de stockage pour C++](https://github.com/Azure/azure-storage-cpp)
- [Bibliothèque de Client de stockage pour Java/Android](/develop/java/)
- [Bibliothèque de Client de stockage pour Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Bibliothèque de Client de stockage pour PHP](/develop/php/)
- [Bibliothèque de Client de stockage pour Ruby](/develop/ruby/)
- [Bibliothèque de Client de stockage pour Python](/develop/python/)
- [Applets de commande de stockage pour PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Services gestion du stockage Azure

- [Référence des API REST stockage ressource fournisseur](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Bibliothèque de Client de fournisseur de ressources de stockage pour .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Applets de commande de stockage ressource fournisseur pour PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [API REST de stockage Service gestion (classique)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Services de déplacement de données de stockage Azure

- [API REST de stockage importer/exporter Service](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Bibliothèque de Client de mouvement de données de stockage pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Outils et utilitaires

- [Explorateur de stockage Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Outils clients stockage Azure](storage-explorers.md)
- [Outils et SDK azure](https://azure.microsoft.com/tools/)
- [Émulateur de stockage Azure](http://www.microsoft.com/download/details.aspx?id=43709)
- [PowerShell Azure](../powershell-install-configure.md)
- [Utilitaire AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le stockage Azure, Explorez ces ressources :

### <a name="documentation"></a>Documentation

- [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>Pour les administrateurs

- [À l’aide de PowerShell Azure avec stockage Azure](storage-powershell-guide-full.md)
- [À l’aide d’Azure infrastructure du langage commun avec stockage Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Pour les développeurs .NET

- [Prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
- [Prise en main stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
- [Prise en main stockage Azure file d’attente à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
- [Prise en main le stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Pour les développeurs Java/Android

- [L’utilisation de stockage d’objets Blob à partir de Java](storage-java-how-to-use-blob-storage.md)
- [Comment utiliser le stockage de Table à partir de Java](storage-java-how-to-use-table-storage.md)
- [Comment utiliser le stockage de file d’attente à partir de Java](storage-java-how-to-use-queue-storage.md)
- [Comment utiliser le stockage de fichiers à partir de Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Pour les développeurs Node.js

- [Comment utiliser le stockage Blob de Node.js](storage-nodejs-how-to-use-blob-storage.md)
- [Comment utiliser le stockage de Table à partir de Node.js](storage-nodejs-how-to-use-table-storage.md)
- [Comment utiliser le stockage de Node.js file d’attente](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Pour les développeurs PHP

- [L’utilisation de stockage d’objets Blob à partir de PHP](storage-php-how-to-use-blobs.md)
- [Comment utiliser le stockage de Table à partir de PHP](storage-php-how-to-use-table-storage.md)
- [Comment utiliser le stockage de file d’attente à partir de PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Pour les développeurs prononciation

- [Comment utiliser le stockage Blob de Ruby](storage-ruby-how-to-use-blob-storage.md)
- [Comment utiliser le stockage de Table à partir de Ruby](storage-ruby-how-to-use-table-storage.md)
- [Comment utiliser le stockage de Ruby file d’attente](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Pour les développeurs Python

- [Comment utiliser le stockage Blob de Python](storage-python-how-to-use-blob-storage.md)
- [Comment utiliser le stockage de Table à partir de Python](storage-python-how-to-use-table-storage.md)
- [Comment utiliser le stockage de Python file d’attente](storage-python-how-to-use-queue-storage.md)
- [Comment utiliser le stockage de fichiers à partir de Python](storage-python-how-to-use-file-storage.md)
