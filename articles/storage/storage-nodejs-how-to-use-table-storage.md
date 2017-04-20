<properties
    pageTitle="Comment utiliser le stockage de Table Azure de Node.js | Microsoft Azure"
    description="Stocker des données structurées dans le cloud à l’aide de stockage de Table Azure, un magasin de données NoSQL."
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


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Comment utiliser le stockage de Table Azure de Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Cette rubrique montre comment effectuer des scénarios courants au moyen du service de Table Azure dans une application Node.js.

Les exemples de code dans cette rubrique part du principe que vous disposez déjà d’une application Node.js. Pour plus d’informations sur la façon de créer une application Node.js dans Azure, voir une des rubriques suivantes :

- [Créer une application web Node.js dans le Service d’application Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Créer et déployer une application Node.js auprès d’un Service Cloud Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (à l’aide de Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>Configurez votre application pour accéder au stockage Azure

Pour utiliser le stockage Azure, vous devez le Kit de développement de stockage Azure pour Node.js, qui comprend un ensemble de bibliothèques de commodité communiquer avec les services reste de stockage.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utiliser le Gestionnaire de Package de nœud (NPM) pour installer le package

1.  Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix) et accédez au dossier où vous avez créé votre application.

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier si une **nœud\_modules** dossier a été créé. Dans ce dossier, vous trouverez le package **stockage azure** , qui contient les bibliothèques que vous avez besoin pour accéder au stockage.

### <a name="import-the-package"></a>Importer le package

Ajoutez le code suivant en haut du fichier **server.js** dans votre application :

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Le module azure lira les variables d’environnement AZURE\_stockage\_compte et AZURE\_stockage\_ACCESS\_clé ou AZURE\_stockage\_connexion\_chaîne pour plus d’informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définis, vous devez spécifier les informations de compte lors de l’appel **TableService**.

Pour obtenir un exemple de définition des variables d’environnement dans le [Portail Azure](https://portal.azure.com) pour un site Web Azure, voir [l’aide du Service de Table Azure Node.js web app].

## <a name="create-a-table"></a>Créer une table

Le code suivant crée un objet **TableService** et utilise pour créer une nouvelle table. Ajoutez le code suivant dans la partie supérieure de **server.js**.

    var tableSvc = azure.createTableService();

L’appel vers **createTableIfNotExists** crée une nouvelle table portant le nom spécifié s’il n’existe pas déjà. L’exemple suivant crée une table nommée « mytable » s’il n’existe pas déjà :

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

La `result.created` sera `true` si une nouvelle table est créée, et `false` si la table existe déjà. La `response` contient des informations sur la demande.

### <a name="filters"></a>Filtres

Opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées via **TableService**. Opérations de filtrage peuvent inclure journalisation, automatiquement une nouvelle tentative, etc.. Filtres sont des objets qui implémentent une méthode avec la signature :

    function handle (requestOptions, next)

Après avoir effectué son prétraitement sur les options de requête, la méthode doit appeler « suivant », en passant un rappel avec la signature suivante :

    function (returnObject, finalCallback, next)

Dans ce rappel et après le traitement de la returnObject (la réponse à la demande sur le serveur), le rappel a besoin appeler ensuite s’il existe pour poursuivre le traitement des autres filtres ou simplement appeler dans le cas contraire finalCallback pour mettre fin à l’appel de service.

Deux filtres qui implémentent la logique de nouvelle tentative sont inclus dans le Kit de développement Azure pour Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **TableService** qui utilise **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité, tout d’abord créer un objet qui définit les propriétés de votre entité. Toutes les entités doivent contenir un **PartitionKey** et **RowKey**, qui sont des identificateurs uniques pour l’entité.

* **PartitionKey** - détermine la partition stockée dans l’entité

* **RowKey** - identifie de façon unique l’entité au sein de la partition

**PartitionKey** et **RowKey** doivent être des valeurs de chaîne. Pour plus d’informations, voir [Présentation du modèle de données de Service Table](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Voici un exemple de la définition d’une entité. Notez que **dueDate** est définie comme un type de **Edm.DateTime**. Spécifier le type est facultative, et types seront déduits si n’est pas spécifiés.

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] Vous trouverez également un champ **d’horodatage** pour chaque enregistrement, ce qui est défini par Azure lorsqu’une entité est insérée ou mis à jour.

Vous pouvez également utiliser **entityGenerator** pour créer des entités. L’exemple suivant crée la même entité de tâche à l’aide de **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Pour ajouter une entité à votre tableau, passez l’objet d’entité à la méthode **insertEntity** .

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

Si l’opération réussit, `result` contiendra l' [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) de l’enregistrement inséré et `response` contient des informations sur l’opération.

Réponse d’exemple :

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] Par défaut, **insertEntity** ne renvoie pas l’entité insérée dans le cadre de la `response` informations. Si vous envisagez d’exécuter d’autres opérations sur cette entité, ou que vous souhaitez mettre en cache les informations, il peut être utile afin que nous retournées dans le cadre de la `result`. Vous pouvez effectuer ceci en activant **echoContent** comme suit :
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Mettre à jour une entité

Il existe plusieurs méthodes disponibles pour mettre à jour une entité existante :

* **replaceEntity** - met à jour une entité existante en remplaçant

* **mergeEntity** - met à jour une entité existante en fusionnant les nouvelles valeurs de propriété dans l’entité existante

* **insertOrReplaceEntity** - met à jour une entité existante en le remplaçant. Si aucune entité n’existe, une nouvelle visualisation est insérée

* **insertOrMergeEntity** - met à jour une entité existante en fusionnant les nouvelles valeurs de propriété dans l’objet existant. Si aucune entité n’existe, une nouvelle visualisation est insérée

L’exemple suivant illustre la mise à jour une entité à l’aide de **replaceEntity**:

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] Par défaut, mettre à jour une entité ne vérifie pas pour voir si les données mis à jour a déjà été modifiées par un autre processus. Pour prendre en charge les mises à jour simultanées :
>
> 1. Obtenir l’ETag de l’objet mis à jour. Ceci est retourné dans le cadre de la `response` pour n’importe quelle entité opération concernant et peuvent être récupérée via `response['.metadata'].etag`.
>
> 2. Lorsque vous effectuez une opération de mise à jour sur une entité, ajoutez les informations ETag précédemment récupérées à la nouvelle entité. Par exemple :
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. Exécuter l’opération de mise à jour. Si l’entité a été modifiée dans la mesure où vous avez extrait la valeur ETag, tel qu’une autre instance de votre application, un `error` apparaîtront indiquant que la condition de mise à jour spécifiée dans la demande a été satisfaite pas.

Si l’entité est mis à jour n’existe pas, avec **replaceEntity** et **mergeEntity**, l’opération de mise à jour va échouer. Par conséquent, si vous souhaitez stocker une entité, indépendamment de si elle existe déjà, utilisez **insertOrReplaceEntity** ou **insertOrMergeEntity**.

La `result` pour la mise à jour réussie opérations contiendra l' **Etag** de l’entité mis à jour.

## <a name="work-with-groups-of-entities"></a>Utilisation des groupes d’entités

Parfois, il est préférable d’envoyer plusieurs opérations collaborer dans un lot pour garantir atomique traitement par le serveur. Pour ce faire, utilisez la classe **TableBatch** pour créer un lot et puis utilisez la méthode **executeBatch** de **TableService** pour effectuer les opérations par lots.

 L’exemple suivant illustre l’envoi de deux entités dans un lot :

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Pour les opérations de lot réussie, `result` contient des informations pour chaque opération dans le lot.

### <a name="work-with-batched-operations"></a>Utilisation des opérations par lots

Opérations ajoutées à un lot peuvent être examinées par l’affichage du `operations` propriété. Vous pouvez également utiliser les méthodes suivantes pour travailler avec les opérations :

* **désactivez** - efface toutes les opérations d’un lot

* **getOperations** - Obtient une opération du lot

* **hasOperations** - renvoie VRAI si le lot contient des opérations

* **removeOperations** - supprime une opération

* **taille** : renvoie le nombre d’opérations du lot

## <a name="retrieve-an-entity-by-key"></a>Récupérer une entité par clé

Pour renvoyer une entité spécifique en fonction du **PartitionKey** et **RowKey**, utilisez la méthode **retrieveEntity** .

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Une fois cette opération terminée, `result` contiendra l’entité.

## <a name="query-a-set-of-entities"></a>Interroger un ensemble d’entités

Pour interroger un tableau, utilisez l’objet **TableQuery** pour créer une expression de requête en utilisant les clauses suivantes :

* **Sélectionnez** - les champs à renvoyer à partir de la requête

* **où** - where clause

    * **et** - un `and` condition where

    * **ou** - un `or` condition where

* **haut** - le nombre d’éléments à récupérer


L’exemple suivant crée une requête qui renvoie les cinq premiers éléments avec un PartitionKey de « hometasks ».

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Dans la mesure où **Sélectionner** n’est pas utilisé, tous les champs seront renvoyées. Pour exécuter la requête sur une table, utilisez **queryEntities**. L’exemple suivant utilise cette requête pour renvoyer les entités 'mytable'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

En cas de réussite, `result.entries` contiendra un tableau d’entités qui correspondent à la requête. Si la requête n’a pas pu renvoyer toutes les entités, `result.continuationToken` sera non -*null* et peut être utilisé comme le troisième paramètre de **queryEntities** pour extraire les résultats plus. Pour la requête initiale, utilisez *null* pour le troisième paramètre.

### <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête

Une requête à une table peut extraire des champs quelques d’une entité.
Cela permet de réduire la bande passante et peut améliorer les performances des requêtes, en particulier pour les entités volumineuses. Utilisez la clause **select** et passez les noms des champs à renvoyer. Par exemple, la requête suivante renvoie uniquement les champs **description** et **dueDate** .

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>Supprimer une entité

Vous pouvez supprimer une entité à l’aide de ses clés partition et ligne. Dans cet exemple, l’objet **Tâche1** contient les valeurs **RowKey** et **PartitionKey** de l’entité à supprimer. Ensuite, l’objet est passé à la méthode **deleteEntity** .

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] Envisagez d’utiliser des ETags lors de la suppression d’éléments, pour vous assurer que l’élément n’a pas été modifié par un autre processus. Pour plus d’informations sur l’utilisation de ETags, voir [mettre à jour une entité](#update-an-entity) .

## <a name="delete-a-table"></a>Supprimer un tableau

Le code suivant supprime un tableau à partir d’un compte de stockage.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Si vous ne savez pas si la table existe, utilisez **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Utiliser les jetons de continuation

Lorsque vous interrogez tables de grandes quantités de résultats, recherchez les jetons de continuation. Grandes quantités de données peut être disponible pour votre requête que vous avez ne peut-être pas réalisé si vous ne créez pas reconnaître lorsqu’un jeton de continuation est présent.

Les résultats renvoyés au cours d’interrogation de jeux d’entités d’objet un `continuationToken` propriété lorsque ce jeton est présent. Vous pouvez ensuite utiliser ce lors d’une requête pour continuer à déplacer entre les entités partition et table.

Lors de l’interrogation, un paramètre continuationToken peut être fourni entre l’instance d’objet requête et la fonction de rappel :

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Si vous examinez le `continuationToken` objet, vous trouverez propriétés telles que `nextPartitionKey`, `nextRowKey` et `targetLocation`, qui peuvent servir à parcourir tous les résultats.

Vous trouverez également un exemple de continuation dans le mis en pension Azure stockage Node.js sur GitHub. Recherchez `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Utiliser des signatures accès partagé

Accès partagé signatures (sa) constituent un moyen sécurisé pour fournir un accès précis aux tables sans fournir votre nom de compte de stockage ou des clés. Associations de sécurité sont souvent utilisées pour fournir un accès limité à vos données, telles que ce qui permet une application mobile pour interroger les enregistrements.

Une application de confiance comme un service en nuage génère une associations de sécurité à l’aide de la **generateSharedAccessSignature** de la **TableService**et fournit à l’application non approuvée ou semi-structurées approuvée comme une application mobile. Les associations de sécurité sont générée à l’aide d’une stratégie, qui décrit les dates de début et de fin pendant lequel les associations de sécurité sont valide, ainsi que le niveau d’accès accordé au titulaire du associations de sécurité.

L’exemple suivant génère une stratégie d’accès partagé qui permet d’autoriser le titulaire associations de sécurité à la requête (« r ») la table et arrive à expiration 100 minutes après l’heure de que sa création.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Notez que les informations sur l’hôte doivent être fournies également, qu’il est requis lorsque le titulaire associations de sécurité tente d’accéder à la table.

L’application cliente utilise les associations de sécurité avec **TableServiceWithSAS** pour effectuer des opérations sur la table. L’exemple suivant se connecte à la table et exécute une requête.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Étant donné que les associations de sécurité a été générée à l’aide d’accès de requête, si une tentative d’ont été apportée à insérer, mettre à jour ou supprimer des entités, une erreur est renvoyée.

### <a name="access-control-lists"></a>Listes de contrôle d’accès

Vous pouvez également utiliser une liste de contrôle avec mise en forme (accès) pour définir la stratégie d’accès pour un sa. Ceci est utile si vous souhaitez permettre à plusieurs clients à accéder à la table, mais fournit des stratégies d’accès différentes pour chaque client.

Un et est activée à l’aide d’un tableau des stratégies d’accès, dont l’ID est associé à chaque stratégie. L’exemple suivant définit deux stratégies, une pour l’utilisateur '1' et l’autre pour l’utilisateur « 2 » :

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

L’exemple suivant obtient l’et actuel pour la table **hometasks** et puis ajoute les nouvelles stratégies à l’aide de **setTableAcl**. Cette approche permet de :

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Une fois que l’et a été défini, vous pouvez ensuite créer une associations de sécurité en fonction du code pour une stratégie. L’exemple suivant crée une nouvelles associations de sécurité pour l’utilisateur « 2 » :

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes.

-   [Blog de l’équipe stockage azure][].
-   [Kit de développement logiciel pour nœud stockage Azure][] référentiel GitHub.
-   [Centre de développement Node.js](/develop/nodejs/)

  [Kit de développement pour le nœud de stockage Azure]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Dans le navigateur Node.js au moyen du Service de Table Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
