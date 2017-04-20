<properties
  pageTitle="Didacticiel NoSQL Node.js pour DocumentDB | Microsoft Azure"
  description="Didacticiel NoSQL Node.js qui crée une application de base de données et console nœud à l’aide du Kit de développement Node.js DocumentDB. DocumentDB est une base de données NoSQL JSON."
    keywords="didacticiel Node.js, base de données nœud"
  services="documentdb"
  documentationCenter="node.js"
  authors="AndrewHoh"
  manager="jhubbard"
  editor="monicar"/>

<tags
  ms.service="documentdb"
  ms.workload="data-services"
  ms.tgt_pltfrm="na"
  ms.devlang="node"
  ms.topic="hero-article"
  ms.date="08/11/2016"
  ms.author="anhoh"/>

# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>Didacticiel NoSQL Node.js : application console DocumentDB Node.js  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenue dans le didacticiel Node.js pour le Kit de développement Azure DocumentDB Node.js ! Après avoir suivi ce didacticiel, vous avez une application console qui crée et requêtes DocumentDB ressources, y compris une base de données de nœud.

Nous aborderons :

- Création et la connexion à un compte DocumentDB
- La configuration de votre application
- Création d’une base de données de nœud
- Création d’une collection de sites
- Création de documents JSON
- Interroger la collection de sites
- Remplacement d’un document
- Suppression d’un document
- Suppression de la base de données de nœud

Ne pas avoir le temps ? Ne vous inquiétez pas ! Une solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Pour obtenir des instructions rapides, voir [obtenir une solution complète](#GetSolution) .

Une fois que vous avez terminé le didacticiel Node.js, utilisez les boutons de vote en haut et bas de cette page pour nous envoyer des commentaires. Si vous souhaitez que nous vous contacter directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Maintenant prise en main !

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Conditions préalables pour le didacticiel Node.js

Vérifiez que vous disposez des éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire à une [Version d’évaluation gratuite de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) version v0.10.29 ou une version ultérieure.

## <a name="step-1-create-a-documentdb-account"></a>Étape 1 : Créer un compte DocumentDB

Créer un compte DocumentDB. Si vous possédez déjà un compte que vous souhaitez utiliser, vous pouvez passer à [configurer votre application Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>Étape 2 : Configurer votre application Node.js

1. Ouvrez votre terminal favori.
2. Recherchez le dossier ou le répertoire l’endroit où vous voulez enregistrer votre application Node.js.
3. Créez deux fichiers JavaScript vides avec les commandes suivantes :
  - Windows :
      * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
  - Linux/OS x :
      * ```touch app.js```
        * ```touch config.js```
4. Installez le module documentdb via npm. Utilisez la commande suivante :
    * ```npm install documentdb --save```

Rédaction ! Maintenant que vous avez terminé la configuration, commençons écrire du code.

## <a id="Config"></a>Étape 3 : Définir les configurations de votre application

Ouvrir ```config.js``` dans votre éditeur de texte.

Ensuite, copier et coller l’extrait de code ci-dessous et définir les propriétés ```config.endpoint``` et ```config.primaryKey``` à votre uri de point de terminaison DocumentDB et la clé primaire. Vous trouverez les deux ces configurations dans le [Portail Azure](https://portal.azure.com).

![Didacticiel Node.js - capture d’écran du portail Azure, avec un compte DocumentDB, avec le hub ACTIVE mis en surbrillance, le bouton de touches apparaît en surbrillance sur la carte de compte DocumentDB et les valeurs URI, clé primaire et clé secondaire mis en évidence sur la carte de clés - base de données nœud][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copier et coller le ```database id```, ```collection id```, et ```JSON documents``` à votre ```config``` objet ci-dessous, où vous avez défini votre ```config.endpoint``` et ```config.authKey``` propriétés. Si vous avez déjà des données que vous voulez stocker dans votre base de données, vous pouvez utiliser [l’outil de Migration de données](documentdb-import-data.md) de DocumentDB plutôt que d’ajouter les définitions de document.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


La base de données, collection de sites et les définitions de document servira votre DocumentDB ```database id```, ```collection id```et les données des documents.

Pour finir, exporter votre ```config``` objet, de sorte que vous pouvez faire référence dans la ```app.js``` fichier.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

##<a id="Connect"></a>Étape 4 : Se connecter à un compte DocumentDB

Ouvrez votre vide ```app.js``` fichier dans l’éditeur de texte. Copiez et collez le code ci-dessous pour importer les ```documentdb``` module et votre nouvellement créé ```config``` module.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copiez et collez le code pour utiliser enregistré précédemment ```config.endpoint``` et ```config.primaryKey``` pour créer un nouveau DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Maintenant que vous avez le code d’initialisation du client documentdb, examinons une utilisation des ressources DocumentDB.

## <a name="step-5-create-a-node-database"></a>Étape 5 : Créer une base de données de nœud
Copiez et collez le code ci-dessous pour définir le statut HTTP pour introuvable, l’url de base de données et l’url de la collection de sites. Ces URL est comment le client DocumentDB recherche la base de données appropriée et collection de sites.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Une [base de données](documentdb-resources.md#databases) pouvant être créées à l’aide de la fonction [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient** . Une base de données est le conteneur logique du stockage des documents partitionnée provenant de plusieurs collections.

Copier et coller la fonction **getDatabase** pour la création de votre nouvelle base de données dans le fichier app.js avec la ```id``` spécifié dans le ```config``` objet. La fonction vérifie si la base de données avec la même ```FamilyRegistry``` id n’existe pas déjà. S’il existe, nous allons retourner cette base de données au lieu de créer un nouvel identifiant.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copiez et collez le code ci-dessous à l’endroit où vous définissez la fonction **getDatabase** pour ajouter la d’assistance fonction **Quitter** qui imprime le message de sortie et l’appel de fonction **getDatabase** .

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Dans votre terminal, recherchez votre ```app.js``` et exécutez la commande :```node app.js```

Félicitations ! Vous avez créé une base de données DocumentDB.

##<a id="CreateColl"></a>Étape 6 : Créer une collection de sites  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** crée une nouvelle collection, c'est-à-dire tarifs implications. Pour plus d’informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).

Une [collection de sites](documentdb-resources.md#collections) peut être créés à l’aide de la fonction [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient** . Une collection de sites est un conteneur de documents JSON et logique de l’application JavaScript associée.

Copier et coller la fonction **getCollection** en dessous de la fonction **getDatabase** pour la création de votre nouvelle collection avec la ```id``` spécifié dans le ```config``` objet. Là encore, nous allons vérifier pour vous assurer une collection de sites avec le même ```FamilyCollection``` id n’existe pas déjà. S’il existe, nous allons retourner cette collection au lieu de créer un nouvel identifiant.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copiez et collez le code ci-dessous l’appel vers **getDatabase** pour exécuter la fonction **getCollection** .

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Dans votre terminal, recherchez votre ```app.js``` et exécutez la commande :```node app.js```

Félicitations ! Vous avez créé une collection de sites DocumentDB.

##<a id="CreateDoc"></a>Étape 7 : Créer un document
Un [document](documentdb-resources.md#documents) peut être créé à l’aide de la fonction [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient** . Les documents sont définies par l’utilisateur du contenu JSON (arbitraire). Vous pouvez maintenant insérer un document dans DocumentDB.

Copier et coller la fonction **getFamilyDocument** en dessous de la fonction **getCollection** pour la création de documents contenant les données JSON enregistrées dans le ```config``` objet. Là encore, nous allons vérifier pour vous assurer qu'un document avec le même id n’existe pas déjà.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copiez et collez le code ci-dessous l’appel vers **getCollection** pour exécuter la fonction **getFamilyDocument** .

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Dans votre terminal, recherchez votre ```app.js``` et exécutez la commande :```node app.js```

Félicitations ! Vous avez créé un dossier de documents DocumentDB.

![Base de données Node.js tutorial - diagramme illustrant la relation hiérarchique entre le compte, la base de données, la collection de sites et les documents - nœud](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Étape 8 : Interroger DocumentDB ressources

DocumentDB prend en charge [des requêtes enrichies](documentdb-sql-query.md) sur JSON documents stockés dans chaque collection de sites. Le code suivant montre une requête que vous pouvez exécuter sur les documents dans votre collection de sites.

Copiez et collez la fonction **queryCollection** en dessous de la fonction **getFamilyDocument** . DocumentDB prend en charge les requêtes SQL comme illustré ci-dessous. Pour plus d’informations sur la création de requêtes complexes, consultez le [Laboratoire de requête](https://www.documentdb.com/sql/demo) et la [documentation de la requête](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Le diagramme suivant illustre comment la syntaxe de la requête SQL DocumentDB est appelée par rapport à la collection de sites que vous avez créé.

![Base de données Node.js tutorial - diagramme illustrant la portée et ce qui signifie que de la requête - nœud](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Le mot-clé [FROM](documentdb-sql-query.md#from-clause) est facultatif dans la requête, car les requêtes de DocumentDB sont déjà associées à une collection unique. Par conséquent, « De familles f » peut être remplacée par « À partir de racine r », ou toute autre variable nom que vous choisissez. DocumentDB sera déduire cette familles, racine ou le nom de la variable que vous avez choisi, faire référence à la collection actuelle par défaut.

Copiez et collez le code ci-dessous l’appel vers **getFamilyDocument** pour exécuter la fonction **queryCollection** .

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Dans votre terminal, recherchez votre ```app.js``` et exécutez la commande :```node app.js```

Félicitations ! Vous avez correctement interrogé DocumentDB documents.

##<a id="ReplaceDocument"></a>Étape 9 : Remplacez un document
DocumentDB prend en charge le remplacement documents JSON.

Copiez et collez la fonction **replaceDocument** en dessous de la fonction **queryCollection** .

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiez et collez le code ci-dessous l’appel vers **queryCollection** pour exécuter la fonction **replaceDocument** . En outre, ajoutez le code pour appeler **queryCollection** à nouveau pour vérifier que le document a été modifiée.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Dans votre terminal, recherchez votre ```app.js``` et exécutez la commande :```node app.js```

Félicitations ! Vous avez correctement remplacé un document DocumentDB.

##<a id="DeleteDocument"></a>Étape 10 : Supprimer un document
DocumentDB prend en charge les documents JSON suppression.

Copiez et collez la fonction **deleteDocument** en dessous de la fonction **replaceDocument** .

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiez et collez le code ci-dessous l’appel vers la deuxième **queryCollection** pour exécuter la fonction **deleteDocument** .

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Dans votre terminal, recherchez votre ```app.js``` et exécutez la commande :```node app.js```

Félicitations ! Vous avez supprimé un document DocumentDB.

##<a id="DeleteDatabase"></a>Étape 11 : Supprimer la base de données de nœud

La base de données supprimer la base de données et toutes les ressources enfants (collections de sites, documents, etc.).

Copier et coller l’extrait de code suivantes (fonction **nettoyage**) pour supprimer la base de données et toutes les ressources enfants.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copiez et collez le code ci-dessous l’appel vers **deleteDocument** pour exécuter la fonction de **nettoyage** .

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a>Étape 12 : Exécuter votre application Node.js ensemble !

Totalement différent, la séquence pour appeler vos fonctions doit ressembler à ceci :

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Dans votre terminal, recherchez votre ```app.js``` et exécutez la commande :```node app.js```

Vous devriez voir le résultat de votre application de mise en route get. La sortie doit correspondre le texte d’exemple ci-dessous.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Félicitations ! Vous avez créé que vous avez terminé le didacticiel Node.js et que votre application de console DocumentDB première !

## <a id="GetSolution"></a>Obtenir la solution didacticiel Node.js complète
Pour créer la solution GetStarted qui contient tous les exemples de cet article, vous devez les éléments suivants :

-   [Compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponible sur GitHub.

Installez le module **documentdb** via npm. Utilisez la commande suivante :
* ```npm install documentdb --save```

Ensuite, dans le ```config.js``` de fichiers, mettre à jour les valeurs config.endpoint et config.authKey comme décrit dans [étape 3 : définir les configurations de votre application](#Config).

## <a name="next-steps"></a>Étapes suivantes

-   Vous voulez un échantillon Node.js plus complexe ? Voir [créer une application web Node.js à l’aide de DocumentDB](documentdb-nodejs-application.md).
-  Découvrez comment [un compte DocumentDB moniteur](documentdb-monitor-accounts.md).
-  Exécuter des requêtes sur notre exemple de dataset dans le [Laboratoire de requête](https://www.documentdb.com/sql/demo).
-  En savoir plus sur le modèle de programmation dans la section développer de la [page de la documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png
