<properties
    pageTitle="Comment utiliser le stockage Blob de Node.js | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec stockage d’objets Blob Azure (stockage d’objets)."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>Comment utiliser le stockage Blob de Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment effectuer des scénarios courants à l’aide de stockage d’objets Blob. Ces exemples sont écrits via l’API Node.js. Les scénarios présentés comprennent comment charger, de la liste, télécharger et supprimer des objets BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Créer une application Node.js

Pour obtenir des instructions sur la façon de créer une application Node.js, voir [créer une application web Node.js dans le Service d’application Azure], [créer et déployer une application Node.js auprès d’un Service Cloud Azure] --à l’aide de Windows PowerShell, ou [créer et déployer une application web Node.js vers Azure à l’aide d’une matrice Web].

## <a name="configure-your-application-to-access-storage"></a>Configurez votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous devez le Kit de développement de stockage Azure pour Node.js, qui comprend un ensemble de bibliothèques de commodité communiquer avec les services reste de stockage.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Nœud Package Manager (NPM) permet d’obtenir le package

1.  Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix), pour accéder au dossier où vous avez créé votre exemple d’application.

2.  Dans la fenêtre de commande, tapez **npm installer stockage azure** . Résultat de la commande est semblable à l’exemple suivant.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier si une **nœud\_modules** dossier a été créé. Dans ce dossier, recherchez le package de **stockage azure** , qui contient les bibliothèques dont vous avez besoin pour accéder au stockage.

### <a name="import-the-package"></a>Importer le package

Utiliser le bloc-notes ou un autre éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Le module Azure lira les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_ACCESS_KEY`, ou `AZURE_STORAGE_CONNECTION_STRING`, pour plus d’informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définis, vous devez spécifier les informations de compte lors de l’appel **createBlobService**.

Pour obtenir un exemple de définition des variables d’environnement dans le [Portail Azure](https://portal.azure.com) pour une application web Azure, voir [l’aide du Service de Table Azure Node.js web app].

## <a name="create-a-container"></a>Créer un conteneur

L’objet **BlobService** vous permet d’utiliser des conteneurs et des objets BLOB. Le code suivant crée un objet **BlobService** . Ajoutez le code suivant dans la partie supérieure de **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Vous pouvez accéder un blob de manière anonyme en utilisant **createBlobServiceAnonymous** et en fournissant l’adresse de l’hôte. Par exemple, utilisez `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Pour créer un nouveau conteneur, utilisez **createContainerIfNotExists**. L’exemple suivant crée un nouveau conteneur nommé « mycontainer » :

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

Si le conteneur est nouvellement créé, `result.created` est vraie. Si le conteneur existe déjà, `result.created` est fausse. `response`contient des informations sur l’opération, y compris les informations ETag pour le conteneur.

### <a name="container-security"></a>Sécurité conteneur

Par défaut, les nouveaux conteneurs sont privés et ne peut pas être accessibles de manière anonyme. Pour publier le conteneur de sorte que vous pouvez y accéder manière anonyme, vous pouvez définir le niveau d’accès du conteneur pour **blob** ou du **conteneur**.

* **objets blob** - autorise l’accès en lecture anonyme blob contenu et des métadonnées dans ce conteneur, mais pas aux métadonnées conteneur telles que tous les objets BLOB dans un conteneur de liste

* **conteneur** - autorise l’accès anonyme en lecture à contenu blob et métadonnées ainsi que les métadonnées de conteneur

L’exemple suivant illustre la définition du niveau d’accès au **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

Par ailleurs, vous pouvez modifier le niveau d’accès d’un conteneur en utilisant **setContainerAcl** pour spécifier le niveau d’accès. L’exemple suivant modifie le niveau d’accès au conteneur :

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

Le résultat contient des informations sur l’opération, notamment l' actif **ETag** pour le conteneur.

### <a name="filters"></a>Filtres

Vous pouvez appliquer des opérations de filtrage facultatives à diverses opérations effectuées via **BlobService**. Opérations de filtrage peuvent inclure journalisation, automatiquement une nouvelle tentative, etc.. Filtres sont des objets qui implémentent une méthode avec la signature :

    function handle (requestOptions, next)

Après avoir effectué son prétraitement sur les options de requête, la méthode doit appeler « suivant », en passant un rappel avec la signature suivante :

    function (returnObject, finalCallback, next)

Dans ce rappel et après le traitement de la returnObject (la réponse à la demande sur le serveur), le rappel a besoin appeler ensuite s’il existe pour poursuivre le traitement des autres filtres ou simplement appeler finalCallback pour mettre fin à l’appel de service.

Deux filtres qui implémentent la logique de nouvelle tentative sont inclus dans le Kit de développement Azure pour Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **BlobService** qui utilise **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Il existe trois types d’objets BLOB : bloquer des objets BLOB, des objets BLOB de page et ajouter des objets BLOB. Objets BLOB bloc autoriser que plus efficacement téléchargement grande quantité de données. Ajouter des objets BLOB sont optimisés pour les opérations d’ajout. Page des objets BLOB sont optimisées pour les opérations de lecture/écriture. Pour plus d’informations, voir [présentation bloc BLOB, ajouter des objets BLOB et des objets BLOB de Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Bloquer des objets BLOB

Pour télécharger des données dans un blob bloc, procédez comme suit :

* **createBlockBlobFromLocalFile** - crée un nouveau blob bloc et télécharge le contenu d’un fichier

* **createBlockBlobFromStream** - crée un nouveau blob bloc et télécharge le contenu d’un flux de données

* **createBlockBlobFromText** - crée un nouveau blob bloc et télécharge le contenu d’une chaîne

* **createWriteStreamToBlockBlob** - fournit un flux d’écriture dans un blob bloc

L’exemple suivant télécharge le contenu du fichier **test.txt** dans **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

La `result` retournés par ces méthodes contient des informations sur l’opération, telles que l' **ETag** du blob.

### <a name="append-blobs"></a>Ajouter des objets BLOB

Pour télécharger des données dans un nouveau blob Ajout, procédez comme suit :

* **createAppendBlobFromLocalFile** - crée un nouveau blob Ajout et télécharge le contenu d’un fichier

* **createAppendBlobFromStream** - crée un nouveau blob Ajout et télécharge le contenu d’un flux de données

* **createAppendBlobFromText** - crée un nouveau blob Ajout et télécharge le contenu d’une chaîne

* **createWriteStreamToNewAppendBlob** - crée un nouveau blob Ajout, puis propose un flux de données en écriture

L’exemple suivant télécharge le contenu du fichier **test.txt** dans **myappendblob**.

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Pour ajouter un bloc à un blob Ajout existant, procédez comme suit :

* **appendFromLocalFile** - ajouter le contenu d’un fichier à un blob Ajout existant

* **appendFromStream** - ajouter le contenu d’un flux de données pour un blob Ajout existant

* **appendFromText** - ajouter le contenu d’une chaîne à un blob Ajout existant

* **appendBlockFromStream** - ajouter le contenu d’un flux de données pour un blob Ajout existant

* **appendBlockFromText** - ajouter le contenu d’une chaîne à un blob Ajout existant

> [AZURE.NOTE] appendFromXXX API fera une validation côté client pour échouer rapide afin d’éviter unncessary appel à un serveur. appendBlockFromXXX ne.

L’exemple suivant télécharge le contenu du fichier **test.txt** dans **myappendblob**.

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>Page des objets BLOB

Pour télécharger des données dans un blob de page, procédez comme suit :

* **createPageBlob** - crée un nouveau blob de page d’une longueur spécifique

* **createPageBlobFromLocalFile** - crée un nouveau blob de page et des téléchargements le contenu d’un fichier

* **createPageBlobFromStream** - crée un nouveau blob de page et des téléchargements le contenu d’un flux de données

* **createWriteStreamToExistingPageBlob** - fournit un flux d’écriture pour un blob de page existant

* **createWriteStreamToNewPageBlob** - crée un nouveau blob de page, puis propose un flux de données en écriture

L’exemple suivant télécharge le contenu du fichier **test.txt** dans **mypageblob**.

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] Page des objets BLOB sont constitués de 512 octets 'pages'. Vous recevrez un message d’erreur lors du chargement de données dont la taille n’est pas un multiple de 512.

## <a name="list-the-blobs-in-a-container"></a>Répertorier les objets BLOB dans un conteneur

Pour répertorier les objets BLOB dans un conteneur, utilisez la méthode **listBlobsSegmented** . Si vous voulez retourner des objets BLOB avec un préfixe spécifique, utilisez **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

La `result` contient un `entries` collection de sites, qui est un tableau d’objets qui décrivent chaque blob. Si tous les objets BLOB ne peut pas être retournées, le `result` fournit également un `continuationToken`, dont vous pouvez utiliser comme deuxième paramètre pour extraire les entrées supplémentaires.

## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger des données depuis un blob, procédez comme suit :

* **getBlobToLocalFile** - écrit le contenu d’objets blob dans un fichier

* **getBlobToStream** - écrit le contenu d’objets blob dans un flux de données

* **getBlobToText** - écrit le contenu d’objets blob dans une chaîne

* **createReadStream** - fournit un flux à lire à partir du blob

L’exemple suivant illustre l’utilisation de **getBlobToStream** pour télécharger le contenu de l’objet blob **myblob** et le stocker dans le fichier **output.txt** à l’aide d’un flux de données :

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

La `result` contient des informations sur le blob, y compris des informations **ETag** .

## <a name="delete-a-blob"></a>Supprimer un blob

Enfin, pour supprimer un blob, appelez **deleteBlob**. L’exemple suivant supprime le blob nommé **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Accès simultané

Pour prendre en charge d’accès simultané dans un blob à partir de plusieurs clients ou plusieurs instances de processus, vous pouvez utiliser **ETags** ou **location**.

* **Etag** - fournit un moyen de détecter que le blob ou conteneur a été modifié par un autre processus

* **Location** - permet d’obtenir exclusif, renouvelable, écrire ou supprimer l’accès à un objet blob pour une période de temps

### <a name="etag"></a>ETag

Utiliser ETags si vous avez besoin permettre à plusieurs clients ou instances d’écrire sur le bloc de page ou des objets Blob Blob simultanément. L’ETag permet de déterminer si le conteneur ou blob a été modifié dans la mesure où vous lisez initiale ou créé, ce qui vous permet d’éviter de remplacer les modifications validées par un autre client ou un processus.

Vous pouvez définir des conditions ETag à l’aide de l’option `options.accessConditions` paramètre. L’exemple suivant télécharge uniquement le fichier **test.txt** si le blob existe déjà et a la valeur ETag contenus par `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

Lorsque vous utilisez ETags, le modèle général est la suivante :

1. Obtenir l’ETag comme résultat d’une créer, une liste ou une opération get.

2. Effectuer une action, vérifier que la valeur ETag n’a pas été modifiée.

Si la valeur a été modifiée, cela signifie qu’un autre client ou instance modifié le blob ou le conteneur dans la mesure où vous avez obtenu la valeur ETag.

### <a name="lease"></a>Location

Vous pouvez acquérir de nouvelles perspectives en utilisant la méthode **acquireLease** , en spécifiant le blob ou le conteneur que vous souhaitez obtenir une location sur. Par exemple, le code suivant pose une location sur **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

Les opérations suivantes sur **myblob** doivent fournir le `options.leaseId` paramètre. La location ID est retourné comme `result.id` de **acquireLease**.

> [AZURE.NOTE] Par défaut, la durée est infinie. Vous pouvez spécifier une durée non infini (entre 15 et 60 secondes) en fournissant la `options.leaseDuration` paramètre.

Pour supprimer une location, utilisez **releaseLease**. Pour couper une location, mais empêcher d’autres personnes d’obtenir de nouvelles perspectives jusqu'à ce que la durée d’origine a expiré, utilisez **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Utiliser des signatures accès partagé

Accès partagé signatures (sa) constituent un moyen sécurisé pour fournir un accès granulaire à des conteneurs et des objets BLOB sans fournir votre nom de compte de stockage ou des clés. Accès partagé signatures sont souvent utilisés pour fournir un accès limité à vos données, telles que ce qui permet une application mobile accéder aux objets BLOB.

> [AZURE.NOTE] Pendant que vous pouvez également autoriser l’accès anonyme à des objets BLOB, accès partagé signatures permettent de fournir plus un accès contrôlé, que vous devez générer les associations de sécurité.

Une application de confiance comme un service en nuage génère signatures accès partagé à l’aide de la **generateSharedAccessSignature** de la **BlobService**et fournit à l’application non approuvée ou semi-structurées approuvée comme une application mobile. Accès signatures sont générés à l’aide d’une stratégie, qui décrit le début et de fin des dates pendant laquelle les signatures accès partagé sont valides partagé, ainsi que le niveau d’accès accordé au titulaire de signatures du accès partagé.

L’exemple suivant génère une stratégie d’accès partagé qui permet au propriétaire de signatures accès partagé effectuer les opérations de lecture sur le blob **myblob** et arrive à expiration 100 minutes après l’heure de que sa création.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

Notez que les informations sur l’hôte doivent être fournies également, qu’il est requis lorsque le titulaire de signatures accès partagé tente d’accéder au conteneur.

L’application cliente puis utilise des signatures accès partagé avec **BlobServiceWithSAS** pour effectuer des opérations sur l’objets blob. Les éléments suivants Obtient des informations sur **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Dans la mesure où les signatures accès partagé a été générés avec l’accès en lecture seule, si une tentative est faite pour modifier le blob, une erreur est renvoyée.

### <a name="access-control-lists"></a>Listes de contrôle d’accès

Vous pouvez également utiliser une liste de contrôle d’accès (et) pour définir la stratégie d’accès pour les associations de sécurité. Ceci est utile si vous souhaitez permettre à plusieurs clients à accéder à un conteneur mais fournit des stratégies d’accès différentes pour chaque client.

Un et est activée à l’aide d’un tableau des stratégies d’accès, dont l’ID est associé à chaque stratégie. L’exemple suivant définit deux stratégies, une pour l’utilisateur '1' et l’autre pour l’utilisateur « 2 » :

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

L’exemple suivant obtient les listes de contrôle accès pour **mycontainer**et puis ajoute les nouvelles stratégies à l’aide de **setBlobAcl**. Cette approche permet de :

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Une fois que l’et est définie, vous pouvez ensuite créer des signatures accès partagé en fonction de l’ID d’une stratégie. L’exemple suivant crée un nouveau signatures accès partagé pour l’utilisateur « 2 » :

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes.

-   [Kit de développement pour référence de l’API nœud de stockage Azure][]
-   [Blog de l’équipe stockage Azure][]
-   [Kit de développement logiciel pour nœud stockage Azure][] référentiel GitHub
-   [Centre de développement Node.js](/develop/nodejs/)
-   [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

[Kit de développement pour le nœud de stockage Azure]: https://github.com/Azure/azure-storage-node

[Créer une application web Node.js dans le Service d’application Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Dans le navigateur Node.js au moyen du Service de Table Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Créer et déployer une application web Node.js dans Azure à l’aide d’une matrice Web]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Créer et déployer une application Node.js auprès d’un Service Cloud Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement pour référence de l’API nœud de stockage Azure]: http://dl.windowsazure.com/nodestoragedocs/index.html
