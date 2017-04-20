<properties 
    pageTitle="Configurer une chaîne de connexion au stockage Azure | Microsoft Azure"
    description="Configurer une chaîne de connexion à un compte de stockage Azure. Une chaîne de connexion inclut les informations requises pour authentifier l’accès à un compte de stockage à partir de votre application en cours d’exécution."
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
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="configure-azure-storage-connection-strings"></a>Configurer les chaînes de connexion de stockage Azure

## <a name="overview"></a>Vue d’ensemble

Une chaîne de connexion inclut les informations d’authentification nécessaires pour accéder aux données dans un compte de stockage Azure à partir de votre application en cours d’exécution. Vous pouvez configurer une chaîne de connexion à :

- Se connecter à l’émulateur de stockage Azure.
- Accéder à un compte de stockage dans Azure.
- Accéder à des ressources spécifiées dans Azure via une signature accès partagé (sa).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Le stockage de la chaîne de connexion

Votre application devra accèdent à la chaîne de connexion en cours d’exécution pour vous authentifier des demandes faites au stockage Azure. Vous avez quelques différentes options pour le stockage de la chaîne de connexion :

- Pour une exécution de l’application sur le bureau ou sur un appareil, vous pouvez stocker la chaîne de connexion dans un `app.config `fichier ou un `web.config` fichier. Ajouter la chaîne de connexion à la section **AppSettings** .
- Pour une application en cours d’exécution dans un service cloud Azure, vous pouvez stocker la chaîne de connexion dans le [fichier de schéma (.cscfg) de configuration de service Azure](https://msdn.microsoft.com/library/ee758710.aspx). Ajoutez la chaîne de connexion à la section **ConfigurationSettings** du fichier de configuration du service.
- Vous pouvez également utiliser la chaîne de connexion directement dans votre code. La plupart des scénarios, cependant, nous vous recommandons de stocker la chaîne de votre configuration dans un fichier de configuration.

Le stockage de la chaîne de connexion au sein d’un fichier de configuration facilite la mise à jour de la chaîne de connexion pour basculer entre l’émulateur de stockage et un compte de stockage Azure dans le cloud. Vous devez uniquement modifier la chaîne de connexion pour pointer vers votre environnement cible.

Vous pouvez utiliser la classe [Gestionnaire de Configuration de Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) pour accéder à votre chaîne de connexion en cours d’exécution quelle que soit l’endroit où votre application est en cours d’exécution.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Créer une chaîne de connexion à l’émulateur de stockage

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur l’émulateur de stockage, voir [utiliser l’émulateur de stockage Azure de développement et de test](storage-use-emulator.md) .

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Créer une chaîne de connexion à un compte de stockage Azure

Pour créer une chaîne de connexion à votre compte de stockage Azure, utilisez le format de chaîne de connexion ci-dessous. Indiquer si vous voulez vous connecter pour le compte de stockage via HTTPS (recommandé) ou HTTP, remplacez `myAccountName` avec le nom de votre compte de stockage et remplacer `myAccountKey` avec votre clé d’accès compte :

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Par exemple, la chaîne de connexion ressemblera à l’exemple de chaîne de connexion suivants :

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Stockage Azure prend en charge HTTP et HTTPS dans une chaîne de connexion ; Toutefois, à l’aide de HTTPS est fortement recommandé.

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Créer une chaîne de connexion à l’aide d’une signature accès partagé

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Création d’une chaîne de connexion à un point de terminaison de stockage explicite

Vous pouvez spécifier explicitement les points de terminaison de service dans la chaîne de connexion au lieu d’utiliser les points de terminaison par défaut. Pour créer une chaîne de connexion qui spécifie un point de terminaison explicite, spécifiez le point de terminaison du service complet pour chaque service, y compris la spécification du protocole (HTTPS (recommandé) ou HTTP), dans le format suivant :

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

Un scénario où vous souhaitez indiquer un point de terminaison explicite est si vous avez mappé votre point de terminaison du stockage Blob à un domaine personnalisé. Dans ce cas, vous pouvez spécifier votre point de terminaison personnalisé pour le stockage Blob dans votre chaîne de connexion et vous pouvez également spécifier les points de terminaison par défaut pour l’autre service si votre application utilise les.

Voici quelques exemples de chaînes de connexion valides qui spécifient un point de terminaison explicite pour le service Blob :

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

La valeur de point de terminaison qui figure dans la chaîne de connexion est utilisée pour créer la demande URI au service Blob, et il détermine le formulaire de n’importe quel URI qui sont retournés à votre code.

Notez que si vous choisissez d’omettre un point de terminaison de service à partir de la chaîne de connexion, puis vous ne pouvez pas utiliser cette chaîne de connexion pour accéder aux données dans ce service à partir de votre code.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Création d’une chaîne de connexion avec un suffixe de point de terminaison

Pour créer une chaîne de connexion de service de stockage dans les régions ou instances suffixes de point de terminaison différent, par exemple pour Azure Chine ou à la gouvernance Azure, utilisez le format de chaîne de connexion suivant. Indiquer si vous voulez vous connecter pour le compte de stockage via HTTP ou HTTPS, remplacez `myAccountName` avec le nom de votre compte de stockage, remplacez `myAccountKey` touche d’accès rapide compte et remplacer `mySuffix` avec le suffixe URI :


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


Par exemple, la chaîne de connexion doit ressembler à la chaîne de connexion :

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>L’analyse d’une chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>Étapes suivantes

- [Utiliser l’émulateur de stockage Azure de développement et de test](storage-use-emulator.md)
- [Stockage Azure Explorers](storage-explorers.md)
- [Utilisation de Signatures accès partagé (sa)](storage-dotnet-shared-access-signature-part-1.md)
