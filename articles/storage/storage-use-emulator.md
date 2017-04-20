<properties 
    pageTitle="Utiliser l’émulateur de stockage Azure de développement et de test | Microsoft Azure" 
    description="L’émulateur de stockage Azure fournit un environnement de développement local gratuit pour développer et tester le stockage Azure. En savoir plus sur l’émulateur de stockage, y compris la méthode d’authentification demandes, comment se connecter à l’émulateur à partir de votre application et comment utiliser l’outil de ligne de commande." 
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
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utiliser l’émulateur de stockage Azure de développement et de test

## <a name="overview"></a>Vue d’ensemble

L’émulateur de stockage Microsoft Azure fournit un environnement local qui émule les services Blob Azure, file d’attente et de Table pour le développement. À l’aide de l’émulateur de stockage, vous pouvez tester votre application avec les services de stockage localement, sans création d’un abonnement Azure ou engager les coûts. Lorsque vous êtes satisfait de fonctionne de votre application dans l’émulateur, vous pouvez basculer à l’aide d’un compte de stockage Azure dans le cloud.

> [AZURE.NOTE] L’émulateur de stockage est disponible dans le cadre du [Kit de développement Microsoft Azure](https://azure.microsoft.com/downloads/). Vous pouvez également installer l’émulateur de stockage à l’aide du [programme d’installation autonome](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Pour configurer l’émulateur de stockage, vous devez disposer des privilèges d’administrateur sur l’ordinateur.
> 
> L’émulateur de stockage actuellement fonctionne uniquement sur Windows.
>  
> Notez que les données créées dans une version de l’émulateur de stockage ne sont pas assuré d’être accessible lorsque vous utilisez une autre version. Si vous avez besoin conserver vos données sur le long terme, il est recommandé de stocker ces données dans un compte de stockage Azure, plutôt que dans l’émulateur de stockage.

## <a name="how-the-storage-emulator-works"></a>Fonctionne de l’émulateur de stockage
 
L’émulateur de stockage utilise une instance de Microsoft SQL Server locale et le système de fichiers local pour émuler les services de stockage Azure. Par défaut, l’émulateur de stockage utilise une base de données de Microsoft SQL Server 2012 Express LocalDB.  Vous pouvez choisir de configurer l’émulateur de stockage pour accéder à une instance de SQL Server locale au lieu de l’instance LocalDB. Pour plus d’informations, voir [démarrage et initialisation l’émulateur de stockage](#start-and-initialize-the-storage-emulator) en dessous.

Vous pouvez installer SQL Server Management Studio Express pour gérer votre installation LocalDB. L’émulateur de stockage se connecte à SQL Server ou LocalDB à l’aide de l’authentification Windows. 

Il existe quelques différences de fonctionnalités entre émulateur de stockage et les services de stockage Azure. Pour plus d’informations sur ces différences, voir [différences entre l’émulateur de stockage et le stockage Azure](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="authenticating-requests-against-the-storage-emulator"></a>L’authentification des demandes contre l’émulateur de stockage

Comme avec le stockage Azure dans le cloud, toutes les requêtes que vous apportez par rapport à l’émulateur de stockage doivent être authentifié, sauf s’il est une demande anonyme. Vous pouvez authentifier les requêtes par rapport à l’émulateur de stockage en utilisant l’authentification de clé partagée ou avec une signature d’un accès partagé (sa).

### <a name="authentication-with-shared-key-credentials"></a>Authentification avec les informations d’identification de clé partagée

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur les chaînes de connexion, voir [Configurer les chaînes de connexion stockage Azure](storage-configure-connection-string.md). 

### <a name="authentication-with-a-shared-access-signature"></a>Authentification avec une signature accès partagé 

Certaines bibliothèques client stockage Azure, par exemple la bibliothèque Xamarin, ne prennent en charge l’authentification avec un jeton de signature (sa) accès partagé. Vous devrez créer ce jeton associations de sécurité à l’aide d’un outil ou une application qui prend en charge l’authentification de clé partagée. Un moyen facile de générer le jeton associations de sécurité est via PowerShell Azure :

1. Installez PowerShell Azure si vous n’avez pas déjà. Utiliser la dernière version des applets de commande PowerShell Azure est recommandé. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md#Install) pour obtenir des instructions d’installation.

2. Ouvrez Azure PowerShell et exécutez les commandes suivantes. N’oubliez pas de remplacer *ACCOUNT_NAME* et *ACCOUNT_KEY ==* avec vos propres informations d’identification. Remplacez *CONTAINER_NAME* par un nom de votre choix.

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

La signature accès partagé qui en résulte URI pour le nouveau conteneur doit être semblable à ce qui suit :

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La signature d’un accès partagé créée avec cet exemple est valide pour un jour. La signature accorde l’accès complet (c'est-à-dire en lecture, écriture, suppression, liste) à des objets BLOB dans le conteneur.

Pour plus d’informations sur les signatures accès partagé, reportez-vous [à l’aide de l’accès des Signatures (sa partagées)](storage-dotnet-shared-access-signature-part-1.md).


## <a name="start-and-initialize-the-storage-emulator"></a>Démarrer et initialisation l’émulateur de stockage

Pour démarrer l’émulateur de stockage Azure, sélectionnez le bouton Démarrer, ou appuyez sur la touche Windows. Commencez à taper **Émulateur de stockage Azure**, puis sélectionnez l’émulateur dans la liste des applications. 

Lorsque l’émulateur est en cours d’exécution, vous verrez une icône dans la zone de notification de la barre des tâches Windows.

Lorsque l’émulateur de stockage démarre, une fenêtre de ligne de commande s’affiche. Vous pouvez utiliser cette fenêtre de ligne de commande pour démarrer et arrêter l’émulateur de stockage ainsi que les effacer les données, obtenir l’état actuel et initialisation l’émulateur. Pour plus d’informations, voir [Stockage émulateur de ligne de commande outil de référence](#storage-emulator-command-line-tool-reference).

Lorsque la fenêtre de ligne de commande est fermée, l’émulateur de stockage continue de fonctionner. Pour afficher la ligne de commande de nouveau, suivez les étapes ci-dessus comme si le démarrage de l’émulateur de stockage.

La première fois que vous exécutez l’émulateur de stockage, l’environnement de stockage local est initialisé pour vous. Le processus d’initialisation crée une base de données dans LocalDB et réserve ports HTTP pour chaque service de stockage local. 

L’émulateur de stockage est installé par défaut dans le répertoire C:\Program Files (x86) \Microsoft SDKs\Azure\Storage émulateur. 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initialisation l’émulateur de stockage pour utiliser une autre base de données SQL

Vous pouvez utiliser l’outil de ligne de commande de stockage émulateur initialisation l’émulateur de stockage pour pointer vers une instance de base de données SQL autre que celle LocalDB par défaut. Notez que vous devez exécuter l’outil de ligne de commande avec des privilèges d’administrateur pour l’initialisation de la base de données principale pour l’émulateur de stockage :

1. Cliquez sur le bouton **Démarrer** ou appuyez sur la touche **Windows** . Commencez à taper `Azure Storage Emulator` et sélectionnez-le lorsqu’il s’affiche pour faire apparaître l’outil de ligne de commande de stockage émulateur.
2. Dans la fenêtre d’invite de commandes, tapez la commande suivante, où `<SQLServerInstance>` est le nom de l’instance SQL Server. Pour utiliser LocalDb, spécifiez `(localdb)\v11.0` en tant que l’instance SQL Server.

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    Vous pouvez également utiliser la commande suivante, qui indique à l’émulateur pour utiliser l’instance de SQL Server par défaut :

        AzureStorageEmulator init /server .\\ 

    Ou bien, vous pouvez utiliser la commande suivante, qui réinitialise la base de données à l’instance LocalDB par défaut :

        AzureStorageEmulator init /forceCreate 

Pour plus d’informations sur ces commandes, voir [Référence d’outils de ligne de commande stockage émulateur](#storage-emulator-command-line-tool-reference).

## <a name="addressing-resources-in-the-storage-emulator"></a>Ressources d’adressage de dans l’émulateur de stockage

Les points de terminaison de service pour l’émulateur de stockage sont différentes de celles d’un compte de stockage Azure. La différence est due au fait que l’ordinateur local n’effectue pas de résolution du nom de domaine, afin que les points de terminaison émulateur stockage nécessitent une adresse locale au lieu d’un nom de domaine.

Lorsque vous envoyez une ressource dans un compte de stockage Azure, vous utilisez le schéma suivant, où le nom du compte fait partie du nom d’hôte URI et la ressource adoucie fait partie du chemin URI :

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Par exemple, l’URI suivant est une adresse valide pour un blob dans un compte de stockage Azure :

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

Dans l’émulateur de stockage, car l’ordinateur local n’effectue pas de résolution du nom de domaine, le nom du compte fait partie du chemin URI plutôt que le nom d’hôte. Vous utilisez le schéma suivant pour une ressource en cours d’exécution dans l’émulateur de stockage :

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Par exemple, l’adresse suivante peut être utilisée pour accéder à un blob dans l’émulateur de stockage :

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Les points de terminaison de service pour l’émulateur de stockage sont les suivantes :

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Le compte secondaire avec RA GRS d’adressage

Depuis la version 3.1, le compte émulateur stockage prend en charge réplication geo redondants accès en lecture (RA GRS). Pour les ressources de stockage dans le nuage et dans l’émulateur local, vous pouvez accéder à l’emplacement secondaire en ajoutant - secondaire pour le nom du compte. Par exemple, l’adresse suivante peut être utilisée pour accéder à un blob en utilisant le moniteur secondaire en lecture seule dans l’émulateur de stockage :

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Pour la programmation sur le site secondaire avec l’émulateur de stockage, utilisez la bibliothèque de stockage Client pour .NET version 3.2 ou version ultérieure. Consultez la [Bibliothèque Microsoft Azure stockage Client pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) pour plus d’informations.

## <a name="storage-emulator-command-line-tool-reference"></a>Référence du stockage émulateur outil ligne de commande

Depuis la version 3.0, lorsque vous lancez l’émulateur de stockage, vous verrez une ligne de commande fenêtre contextuelle. Utilisez la fenêtre de ligne de commande pour démarrer et arrêter l’émulateur ainsi que pour interroger état et effectuer d’autres opérations.

> [AZURE.NOTE] Si vous avez le Microsoft Azure calculer émulateur installé, une icône de barre d’état système s’affichent lorsque vous lancez l’émulateur de stockage. Un clic droit sur l’icône pour afficher un menu, qui fournit un moyen graphique de démarrer et arrêter l’émulateur de stockage.

### <a name="command-line-syntax"></a>Syntaxe de la ligne de commande

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Options

Pour afficher la liste des options, tapez `/help` à l’invite.

| Option | Description                                                    | Commande                                                                                                 | Arguments                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Démarrer**  | Démarrage de l’émulateur de stockage.                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-en*: démarrer l’émulateur dans le processus actuel au lieu de créer un nouveau processus.                          |
| **Arrêter**   | Arrête l’émulateur de stockage.                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **État** | Permet d’imprimer l’état de l’émulateur de stockage.                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **Effacer**  | Supprime les données de tous les services spécifiés sur la ligne de commande. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *objets BLOB*: efface blob de données. <br/>*file d’attente*: efface les données file d’attente. <br/>*tableau*: efface les données de la table. <br/>*tous les*: efface toutes les données dans tous les services. |
| **Initialisation**   | Effectue une initialisation unique pour configurer l’émulateur.       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server Nomserveur\nominstance*: Spécifie le serveur qui héberge l’instance SQL. <br/>*instanceName - sqlinstance*: Spécifie le nom de l’instance SQL à utiliser dans l’instance du serveur par défaut. <br/>*-forcecreate*: force la création de la base de données SQL, même s’il existe déjà. <br/>*-en*: effectue l’initialisation dans le processus actuel au lieu de génération de processus nouveau. Vous devez lancer le processus en cours avec des autorisations élevées pour effectuer l’initialisation.          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Différences entre l’émulateur de stockage et le stockage Azure

Étant donné que l’émulateur de stockage est un environnement émulé en cours d’exécution dans une instance locale SQL, il existe quelques différences de fonctionnalités entre l’émulateur et un compte de stockage Azure dans le cloud :

- L’émulateur de stockage prend en charge un seul compte fixe et une clé d’authentification connu.

- L’émulateur de stockage n’est pas un service de stockage de fichiers et ne prend pas en charge un grand nombre de clients simultanées.

- Comme décrit dans les [ressources d’adressage dans l’émulateur de stockage](#addressing-resources-in-the-storage-emulator), les ressources sont traités différemment dans l’émulateur de stockage par rapport à un compte de stockage Azure. Cette différence est due au fait que résolution du nom de domaine n’est disponible dans le nuage, mais pas sur l’ordinateur local.

- Depuis la version 3.1, le compte émulateur stockage prend en charge réplication geo redondants accès en lecture (RA GRS). Dans l’émulateur, tous les comptes ont RA-GRS activé, et il y a jamais tout retard entre les réplicas principaux et secondaires. Les opérations d’obtenir les statistiques de Service Blob, obtenir les statistiques de Service file d’attente et obtenir les statistiques de Service Table sont pris en charge sur le compte secondaire et retournera toujours la valeur de la `LastSyncTime` élément de réponse en tant que l’heure actuelle en fonction de la base de données SQL sous-jacente.

    Pour la programmation sur le site secondaire avec l’émulateur de stockage, utilisez la bibliothèque de stockage Client pour .NET version 3.2 ou version ultérieure. Consultez la [Bibliothèque Microsoft Azure stockage Client pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) pour plus d’informations.

- Le service de fichiers et les points de terminaison de service PME protocole ne sont actuellement pas pris en charge dans l’émulateur de stockage.

- L’émulateur de stockage renvoie une erreur VersionNotSupportedByEmulator (code d’état HTTP 400 - demande incorrecte) si vous utilisez une version des services de stockage qui n’est pas encore pris en charge par la version de l’émulateur que vous utilisez.

### <a name="differences-for-blob-storage"></a>Différences de stockage d’objets Blob 

Les différences suivantes s’appliquent au stockage Blob dans l’émulateur :

- Stockage émulateur uniquement prend en charge blob la taille maximale de 2 Go.

- Une opération de placer Blob peut réussir par rapport à un blob qui existe dans l’émulateur de stockage et possède une location active, même si le code de location pas a été spécifié dans le cadre de la demande. 

- Ajouter des objets Blob opérations ne sont pas prises en charge par l’émulateur. Tenter d’effectuer une opération sur un blob Ajout renvoie une erreur FeatureNotSupportedByEmulator (code d’état HTTP 400 - demande incorrecte).

### <a name="differences-for-table-storage"></a>Différences de stockage de Table 

Les différences suivantes s’appliquent au stockage de Table dans l’émulateur :

- Propriétés de date dans le service de Table dans l’émulateur de stockage prennent en charge uniquement la plage pris en charge par SQL Server 2005 (*c'est-à-dire*, elles sont requises pour être postérieure au 1 janvier 1753). Toutes les dates antérieures au 1 janvier 1753 sont modifiées en cette valeur. La précision de dates est limitée à la précision de SQL Server 2005, ce qui signifie que les dates sont précis 1/300th de seconde.

- L’émulateur de stockage prend en charge les valeurs de propriété de clé partition clé et ligne de moins de 512 octets chacune. En outre, la taille totale du nom du compte, le nom de la table et le nom de la propriété de clé gagnante ne peut pas dépasser 900 octets.

- La taille totale de la ligne dans un tableau dans l’émulateur de stockage est limitée aux inférieure à 1 Mo.

- Dans l’émulateur de stockage, les propriétés des données de type `Edm.Guid` ou `Edm.Binary` prend en charge uniquement la `Equal (eq)` et `NotEqual (ne)` des opérateurs de comparaison de requête filtrer chaînes.

### <a name="differences-for-queue-storage"></a>Différences pour le stockage de file d’attente

Il n’existe aucuns différences spécifiques au stockage de file d’attente dans l’émulateur.

## <a name="storage-emulator-release-notes"></a>Notes de mise à jour de stockage émulateur

### <a name="version-45"></a>Version 4.5

- Corrige un bogue qui a provoqué l’initialisation et l’installation de l’émulateur de stockage Échec lors de la base de données de sauvegarde a été renommé.

### <a name="version-44"></a>Version 4.4

- L’émulateur de stockage prend désormais en charge la version 2015-12-11 des services de stockage sur les points de terminaison de service Blob, file d’attente et Table.

- Nettoyage de l’émulateur de stockage des données blob est désormais plus efficace lorsque vous travaillez avec un grand nombre d’objets BLOB.

- Corrige un bogue qui a provoqué conteneur ACL XML à être validées légèrement différente dans le service de stockage en quoi il.

- Corrige un bogue qui parfois max et min signalement de valeurs date/heure dans le fuseau horaire incorrect.

### <a name="version-43"></a>Version 4.3

- L’émulateur de stockage prend désormais en charge la version 2015-07-08 des services de stockage sur les points de terminaison de service Blob, file d’attente et Table.

### <a name="version-42"></a>Version 4.2

- L’émulateur de stockage prend désormais en charge la version 04/2015 / 05 des services de stockage sur les points de terminaison de service Blob, file d’attente et Table.

### <a name="version-41"></a>Version 4.1

- L’émulateur de stockage prend désormais en charge la version 2015-02-21 des services stockage Blob, file d’attente et Table points de terminaison service, à l’exception des nouvelles fonctionnalités Blob ajouter. 

- L’émulateur de stockage renvoie maintenant un message d’erreur significatif si vous utilisez une version des services de stockage qui n’est pas encore pris en charge par cette version de l’émulateur. Nous vous recommandons d’utiliser la dernière version de l’émulateur. Si vous rencontrez une erreur VersionNotSupportedByEmulator (code d’état HTTP 400 - demande incorrecte), téléchargez la dernière version de l’émulateur de stockage.

- Un bogue dans laquelle un course causé table entité données fixe pour être incorrects lors des opérations de fusion concurrents.

### <a name="version-40"></a>Version 4.0

- L’émulateur de stockage exécutable a été renommé *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Version 3.2

- L’émulateur de stockage prend désormais en charge la version 2014-02-14 des services de stockage sur les points de terminaison de service Blob, file d’attente et Table. Notez que points de terminaison de service fichier pas actuellement pris en charge l’émulateur de stockage. Pour plus d’informations sur la version 2014-02-14, consultez [le contrôle de version pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) .

### <a name="version-31"></a>Version 3.1

- Accès en lecture geo redondants stockage (RA GRS) est désormais pris en charge dans l’émulateur de stockage. Les obtenir les statistiques de Service Blob, obtenir les statistiques de Service file d’attente et API obtenir des statistiques du Service de tableau sont pris en charge pour le compte secondaire et retournent toujours la valeur de l’élément de réponse LastSyncTime en tant que l’heure actuelle en fonction de la base de données SQL sous-jacente. Pour la programmation sur le site secondaire avec l’émulateur de stockage, utilisez la bibliothèque de stockage Client pour .NET version 3.2 ou version ultérieure. Pour plus d’informations, consultez la bibliothèque Microsoft Azure stockage Client pour référence .NET.

### <a name="version-30"></a>Version 3.0

- L’émulateur de stockage Azure n’est plus fournie dans le même package que l’émulateur cluster.

- L’interface utilisateur graphique d’émulateur stockage est déconseillée et privilégie une interface de ligne de commande contenant un script. Pour plus d’informations sur l’interface de ligne de commande, voir référence d’outils de ligne de commande stockage émulateur. L’interface graphique continuent à être présents dans la version 3.0, mais elle n’est accessible lorsque l’émulateur de calcul est installé par clic droit sur l’icône de barre d’état système, puis en sélectionnant Afficher stockage émulateur UI.

- Version 2013-08-15 des services de stockage Azure est maintenant entièrement pris en charge. (Précédemment cette version était pris en charge uniquement par stockage émulateur version 2.2.1 Preview.)
