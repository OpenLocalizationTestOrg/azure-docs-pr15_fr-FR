##<a name="create-client"></a>Créer une connexion Client

Créer une connexion client en créant un `WindowsAzure.MobileServiceClient` objet.  Remplacer `appUrl` par l’URL de votre application Mobile.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Utiliser des tableaux

Access ou mettre à jour les données, créer une référence à la table principale. Remplacer `tableName` avec le nom du tableau

```
var table = client.getTable(tableName);
```

Une fois que vous avez une référence de table, vous pouvez travailler avec votre tableau :

* [Requête sur une Table](#querying)
  * [Filtrage des données](#table-filter)
  * [Mise en page de données](#table-paging)
  * [Tri des données](#sorting-data)
* [Insertion de données](#inserting)
* [Modification de données](#modifying)
* [Supprimer des données](#deleting)

###<a name="querying"></a>Comment : interroger une référence de Table

Une fois que vous avez une référence de table, vous pouvez l’utiliser pour rechercher des données sur le serveur.  Requêtes sont effectuées dans une langue « LINQ-j’aime ».
Pour renvoyer toutes les données de la table, utilisez la commande suivante :

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La fonction de réussite est appelée avec les résultats.   N’utilisez pas `for (var i in results)` dans la réussite fonctionnent comme qui doit parcourir les informations qui sont incluses dans les résultats de lorsque d’autres fonctions de requête (tel que `.includeTotalCount()`) sont utilisés.

Pour plus d’informations sur la syntaxe de la requête, consultez la [requête objet].

####<a name="table-filter"></a>Filtrage des données sur le serveur

Vous pouvez utiliser un `where` clause sur la référence de table :

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Vous pouvez également utiliser une fonction qui permet de filtrer l’objet.  Dans ce cas le `this` variable est affectée à l’objet en cours en cours de filtrage.  Les éléments suivants équivaut à l’exemple précédent :

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Mise en page de données

Utiliser les méthodes take() et skip().  Par exemple, si vous souhaitez fractionner le tableau en ligne-100 enregistrements :

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

La `.includeTotalCount()` méthode est utilisée pour ajouter un champ totalCount à l’objet de résultats.  Le champ totalCount est rempli avec le nombre total d’enregistrements qui serait retournée si aucune pagination n’est utilisée.

Vous pouvez ensuite utiliser la variable de pages et certains boutons de l’interface utilisateur pour fournir une liste de la page ; Utilisez loadPage() pour charger les nouveaux enregistrements pour chaque page.  Vous devez implémenter quelconque de la mise en cache pour accélérer l’accès à des enregistrements qui ont déjà été chargés.


####<a name="sorting-data"></a>Comment : retourner les données triées

Utilisez les méthodes de requête .orderBy() ou .orderByDescending() :

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Pour plus d’informations sur l’objet de requête, consultez la [requête objet].

###<a name="inserting"></a>Comment : insérer des données

Créer un objet JavaScript avec la date appropriée et appelez table.insert() asynchrone :

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

En cas d’insertion réussie, l’élément inséré est renvoyé avec les champs supplémentaires sont nécessaires pour les opérations de synchronisation.  Vous devez mettre à jour votre propre cache avec ces informations pour les mises à jour ultérieures.

Notez que le SDK Server Azure Mobile applications Node.js prend en charge schéma dynamiques à des fins de développement.
Dans le cas d’un schéma dynamiques, le schéma de la table est mis à jour à la volée, ce qui vous permet d’ajouter des colonnes à la table en spécifiant les dans une instruction insert seulement ou mettre à jour opération.  Nous vous recommandons de désactiver schéma dynamique avant de passer votre application en production.

###<a name="modifying"></a>Comment : modifier des données

Similaire à la méthode .insert(), vous devez créer un objet de la mise à jour, puis appelez .update().  L’objet de la mise à jour doit contenir l’ID de l’enregistrement à mettre à jour : celle-ci est obtenue lors de la lecture de l’enregistrement ou lorsque vous appelez .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Comment : supprimer des données

Appeler la méthode .del() pour supprimer un enregistrement.  Passez l’ID dans une référence d’objet :

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
