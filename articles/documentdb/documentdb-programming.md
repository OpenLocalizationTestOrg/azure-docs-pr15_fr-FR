<properties 
    pageTitle="Programmation DocumentDB : procédures stockées, déclencheurs de base de données et UDF | Microsoft Azure" 
    description="Découvrez comment utiliser DocumentDB pour écrire des procédures stockées, des déclencheurs de base de données et des fonctions définies par l’utilisateur (UDF) dans JavaScript. Obtenir des conseils de programmation de base de données et bien plus encore." 
    keywords="Déclencheurs de base de données, une procédure stockée, procédure stockée, programme de base de données, la procédure stockée, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programmation côté serveur DocumentDB : procédures stockées, déclencheurs de base de données et UDF

Découvrez comment langue d’Azure DocumentDB intégré, l’exécution de transactions JavaScript permet aux développeurs d’écrire des **procédures stockées**, **déclencheurs** et **les fonctions définies par l’utilisateur** en mode natif dans JavaScript. Cela vous permet d’écrire la logique d’application de programme de base de données qui peut être livrée et exécutée directement sur les partitions de stockage de base de données 

Nous vous recommandons de mise en route en regardant la vidéo suivante, où Andrew Liu fournit une brève introduction au modèle de programmation du DocumentDB côté serveur de base de données. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Ensuite, revenez à cet article, dans laquelle vous découvrirez les réponses aux questions suivantes :  

- Comment écrire une une procédure stockée, déclencheur ou UDF à l’aide d’un code JavaScript ?
- Comment DocumentDB garantit acide ?
- Comment fonctionnent les transactions dans DocumentDB ?
- Quelles sont préalable déclenche et postérieures à la déclenche et comment écrire une ?
- Comment enregistrer et exécuter une procédure stockée, déclencheur ou UDF de manière RESTful à l’aide de HTTP ?
- Quels DocumentDB SDK sont disponibles pour créer et exécuter des procédures stockées, déclencheurs et UDF ?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introduction à la procédure stockée et programmation UDF

Cette approche de *« JavaScript comme un jour moderne T-SQL »* libère les développeurs d’applications à partir de la complexité de type système incohérences et technologies de mappage relationnel objet. Il est également de nombreux avantages intrinsèques qui peuvent être utilisés pour créer des applications complexes :  

-   **Logique de procédure :** JavaScript sous forme de niveau élevé langage de programmation fournit une interface riche et familier pour exprimer la logique métier. Vous pouvez effectuer des séquences complexes d’opérations rapprocher les données.

-   **Transactions atomiques :** DocumentDB garantit que les opérations de base de données effectuées à l’intérieur d’une seule procédure stockée ou déclencheur sont atomique. Cela permet à une application de combiner les opérations associées dans un seul lot afin que toutes les réussissent ou qu’aucune d’elles réussir. 

-   **Performances :** Le fait que JSON est mappé intrinsèque sur le système de type de langage Javascript et est également l’unité de base de stockage dans DocumentDB permet d’obtenir des optimisations comme jattes réalisation du documents JSON dans le pool de tampons et les rendre disponibles à la demande pour le code en cours d’exécution. Il existe plusieurs avantages performances associés à la logique métier livraison la base de données :
    -   Le traitement par lots – les développeurs peuvent regrouper les opérations, telles que les insertions et les envoyer en bloc. La latence du trafic réseau de coûts et la charge store pour créer des transactions distinctes sont réduites considérablement. 
    -   Compilation préalable – DocumentDB précompilation des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur (UDF) afin d’éviter le coût de la compilation JavaScript pour chaque appel. La charge de construction du code octets pour la logique procédural est amortie à une valeur minimale.
    -   Classement – vous avez besoin de nombreuses opérations un effet secondaire (« déclencheur ») qui potentiellement fait référence à un ou plusieurs opérations de stockage secondaire. À l’exception atomicité, il s’agit plus performant lorsque déplacé vers le serveur. 
-   **Encapsulation :** Les procédures stockées peuvent servir à regrouper logique métier présente dans un seul endroit. Cela a deux avantages :
    -   Il ajoute une couche d’abstraction au-dessus des données brutes, qui permet aux architectes de données d’évoluer leurs applications indépendamment à partir des données. Il s’agit particulièrement dans le cas lorsque les données sont sans schéma, en raison des hypothèses fragiles devant être incorporées dans l’application s’ils ont à traiter directement les données.  
    -   Cette abstraction vous permet les entreprises de maintenir la sécurité de leurs données en rationalisant l’accès à partir des scripts.  

La création et l’exécution de déclencheurs de base de données, d’une procédure stockée et d’opérateurs de requête personnalisée est pris en charge par le biais [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)et [client SDK](documentdb-sdk-dotnet.md) dans de nombreuses plateformes notamment .NET, Node.js et JavaScript.

**Que ce didacticiel utilise le [Kit de développement logiciel Node.js avec Q promesses](http://azure.github.io/azure-documentdb-node-q/) ** pour illustrer la syntaxe et l’utilisation des procédures stockées, déclencheurs et UDF.   

## <a name="stored-procedures"></a>Procédures stockées

### <a name="example-write-a-simple-stored-procedure"></a>Exemple : Écrivez une procédure stockée simple 
Commençons par une procédure stockée simple qui retourne une réponse « Hello World ».

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


Procédures stockées sont enregistrées par collection de sites et peuvent être appliquées à n’importe quel document et pièce jointe présente dans cette collection de sites. L’extrait suivant montre comment enregistrer la procédure stockée helloWorld avec une collection de sites. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Une fois la procédure stockée est enregistrée, nous puissions exécuter par rapport à la collection de sites et lire les résultats revenir au niveau du client. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


L’objet contexte donne accès à toutes les opérations pouvant être effectuées sur le stockage DocumentDB, ainsi que l’accès aux objets de demande et de réponse. Dans ce cas, nous avons utilisé l’objet response pour définir le corps de la réponse qui a été envoyé au client. Pour plus d’informations, reportez-vous au [serveur DocumentDB JavaScript documentation SDK](http://azure.github.io/azure-documentdb-js-server/).  

Laissez-nous développer cet exemple et ajoutez que plus de base de données des fonctionnalités associées à la procédure stockée. Procédures stockées peuvent créer, mettre à jour, lire, de la requête et supprimer des documents et des pièces jointes à l’intérieur de la collection de sites.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exemple : Écrire une procédure stockée pour créer un document 
L’extrait suivant montre comment utiliser l’objet de contexte pour interagir avec les ressources DocumentDB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Cette procédure stockée prend comme documentToCreate d’entrée, le corps d’un document à créer dans la collection en cours. Toutes ces opérations sont asynchrones et dépendent des rappels de fonction JavaScript. La fonction de rappel comporte deux paramètres, une pour l’objet error en cas d’échec de l’opération et une pour l’objet créé. Dans le rappel, les utilisateurs peuvent gérer l’exception ou génère une erreur. En cas d’un rappel n’est fourni et une erreur se produit, le runtime DocumentDB génère une erreur.   

Dans l’exemple ci-dessus, le rappel génère une erreur en cas d’échec de l’opération. Dans le cas contraire, il définit l’id du document créé dans le corps de la réponse au client. Voici comment cette procédure stockée est exécutée avec des paramètres d’entrée.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
Notez que cette procédure stockée peut être modifiée pour prendre un tableau d’instances de document comme entrée et créez-les tous dans la même exécution de procédure stockée au lieu de plusieurs requêtes réseau pour chacun d’eux créer individuellement. Cela peut servir à mettre en œuvre un importer en bloc efficace pour DocumentDB (décrite plus loin dans ce didacticiel).   

L’exemple montré comment utiliser des procédures stockées. Nous allons aborder des déclencheurs et des fonctions définies par l’utilisateur (UDF) plus loin dans le didacticiel.

## <a name="database-program-transactions"></a>Programme les transactions de base de données
Transactions dans une base de données typique peuvent être définie comme une séquence d’opérations effectuées comme une seule unité logique de travail. Chaque transaction fournit des **garanties acide**. ACIDE est un acronyme connu quatre propriétés - atomicité, cohérence, isolement et durabilité.  

En bref, atomicité garantit que tout le travail effectué à l’intérieur d’une transaction est considéré comme une seule unité où soit toutes ces sections s’engage ou aucun. La cohérence permet de s’assurer que les données sont toujours dans un état correct interne sur transactions. Isolement garantit que deux transactions n’interfèrent pas avec d’autres – généralement, la plupart des systèmes offrent plusieurs niveaux d’isolement pouvant être utilisées selon les besoins de l’application. Durabilité garantit que toute modification qui est validée dans la base de données seront toujours présente.   

Dans DocumentDB, JavaScript est hébergé dans le même espace mémoire que la base de données. Par conséquent, demandes au sein de procédures stockées et déclencheurs s’exécutent dans la même étendue d’une session de base de données. Cela permet de DocumentDB afin de garantir l’acide pour toutes les opérations qui font partie d’un seul procédure stocké/déclencheur. Considérez la définition de procédure stockée suivante :

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Cette procédure stockée utilise transactions au sein d’une application de jeu aux éléments trade entre deux joueurs en une seule opération. La procédure stockée essaie de lire les deux documents chaque correspondant aux ID du lecteur passé comme un argument. Si les deux documents du lecteur sont trouvées, puis la procédure stockée met à jour les documents en remplaçant leurs éléments. Si des erreurs sont détectées tout au long du processus, il lève une exception JavaScript qui implicitement abandon de la transaction.

Si la collection de la procédure stockée est enregistrée par rapport à est un ensemble de partition unique, puis la transaction est dans l’étendue à tous les docuemnts au sein de la collection de sites. Si la collection de sites est segmenté, les procédures stockées sont exécutées dans la portée de transaction d’une clé de partition unique. Chacune se trouvant exécution de la procédure doit comporter une valeur de clé partition correspondant à la portée de la transaction doit s’exécuter sous. Pour plus d’informations, voir [Partition DocumentDB](documentdb-partition-data.md).

### <a name="commit-and-rollback"></a>Validation et restauration
Transactions sont profondeur et en mode natif intégrées dans le modèle de programmation du DocumentDB JavaScript. À l’intérieur d’une fonction JavaScript, toutes les opérations sont automatiquement renvoyées sous une transaction unique. Si le code JavaScript se termine sans une exception, les opérations de base de données sont validées. En effet, les instructions « TRANSACTION commencer » et « TRANSACTION valider » dans les bases de données relationnelles sont implicites dans DocumentDB.  
 
S’il existe une exception est propagée à partir du script, pour l’exécution de DocumentDB JavaScript restaure la transaction entière. Comme le montre l’exemple précédent, lever une exception équivaut à une opération « restaurer » dans DocumentDB.
 
### <a name="data-consistency"></a>Cohérence des données
Déclencheurs et les procédures stockées sont toujours exécutées sur réplica principal de la collection DocumentDB. Cela s’assurer que lit à l’intérieur stocké la cohérence des procédures offre. Requêtes à l’aide des fonctions définies par l’utilisateur peuvent être exécutées sur le serveur principal ou n’importe quel réplica secondaire, mais nous assurer pour atteindre le niveau de cohérence demandées en choisissant la réplique appropriée.

## <a name="bounded-execution"></a>Exécution limitée
Toutes les opérations DocumentDB doivent se terminer dans le serveur spécifié durée de délai d’expiration de la demande. Cette contrainte s’applique également aux fonctions JavaScript (procédures stockées, déclencheurs et les fonctions définies par l’utilisateur). Si une opération ne se termine pas ce délai, la transaction est annulée. Fonctions JavaScript doivent terminer dans le temps imparti ou implémenter un modèle en fonction de continuation pour lot/reprendre l’exécution.  

Afin de simplifier le développement de procédures stockées et déclencheurs pour gérer les limites de durée, toutes les fonctions sous l’objet de la collection de sites (pour créer, lire, remplacer et supprimer des documents et des pièces jointes) renvoient une valeur booléenne qui indique si cette opération va terminer. Si cette valeur est fausse, il est une indication que le délai est sur le point d’expirer et que la procédure doit renvoyer à la ligne l’exécution des requêtes.  Opérations en file d’attente avant la première opération non acceptées store sont assurée de se terminer si la procédure stockée se termine dans le temps et file d’attente d’autres requêtes.  

Fonctions JavaScript sont également limitées sur la consommation de ressources. DocumentDB réserve débit par collection de sites en fonction de la taille d’un compte de base de données générée. Débit est exprimé en une unité de processeur, de mémoire et de consommation IO appelé unités demande ou RUs centrée réduite. Fonctions JavaScript peuvent utiliser augmenter un grand nombre de RUs quelques instants et rencontrée limitée si la limite de la collection de sites est atteint. Procédures stockées intensives ressource peuvent également être mis en quarantaine pour assurer la disponibilité des opérations de base de données primitif.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exemple : En bloc importation de données dans un programme de base de données
Voici un exemple d’une procédure stockée écrit à l’importation en bloc-documents dans une collection de sites. Notez comment la procédure stockée gère l’exécution délimitée en vérifiant la valeur booléenne valeur de retour à partir de createDocument, puis utilise le nombre de documents insérés dans chaque appel de la procédure stockée pour effectuer le suivi et reprendre l’avancement dans l’ensemble des lots.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Déclencheurs de base de données
### <a name="database-pre-triggers"></a>Déclencheurs avant de base de données
DocumentDB fournit des déclencheurs qui sont exécutées ou déclenchés par une opération sur un document. Par exemple, vous pouvez spécifier un déclencheur préliminaire lorsque vous créez un document – ce déclencheur préliminaire est exécutées avant le document est créé. Voici un exemple de l’utilisation des déclencheurs préliminaire pour valider les propriétés d’un document en cours de création :

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


Et le code d’inscription côté client Node.js correspondant du déclencheur :

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Déclencheurs préalable ne peut pas contenir les paramètres d’entrée. L’objet de demande peut être utilisé pour manipuler le message de demande associé à l’opération. Ici, le déclencheur préliminaire est en cours d’exécution avec la création d’un document et le corps du message de demande contient le document doit être créé au format JSON.   

Lorsque des déclencheurs sont enregistrés, les utilisateurs peuvent spécifier les opérations qu’il peut s’exécuter avec. Ce déclencheur a été créé avec TriggerOperation.Create, ce qui signifie que les éléments suivants ne sont pas autorisé.

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Déclencheurs postérieures à la base de données
Déclencheurs après, comme déclencheurs avant, sont associés à une opération sur un document et ne prennent pas tous les paramètres d’entrée. Ils s’exécutent **une fois** que l’opération terminée et ont accès au message de réponse est envoyé au client.   

L’exemple suivant illustre déclencheurs après en action :

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Le déclencheur peut être enregistré comme le montre l’exemple suivant.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Ce déclencheur des requêtes pour le document de métadonnées et met à jour avec les détails concernant le document nouvellement créé.  

Ce qui est important de noter est l’exécution des déclencheurs dans DocumentDB **transactions** . Ce déclencheur après s’exécute dans le cadre de la même transaction que la création du document d’origine. Par conséquent, si nous lever une exception à partir du déclencheur après (par exemple si nous ne pouvons pas mettre à jour le document de métadonnées), la transaction complète échouera et être annulée. Aucun document n’est créé et une exception sera renvoyée.  

##<a id="udf"></a>Fonctions définies par l’utilisateur
Fonctions définies par l’utilisateur (UDF) sont utilisées pour étendre la grammaire du langage de requête DocumentDB SQL et mettre en œuvre la logique métier personnalisée. Ils peuvent seulement être appelés depuis à l’intérieur de requêtes. Ils n’ont pas accès à l’objet de contexte et sont destinés à être utilisés en tant que JavaScript cluster uniquement. Par conséquent, UDF peuvent être exécutés sur les doubles secondaires du service DocumentDB.  
 
L’exemple suivant crée une fonction pour calculer des impôts basées sur les taux des crochets revenus différentes et utilise ensuite à l’intérieur d’une requête pour trouver toutes les personnes qui payé plus de 20 000 $ dans taxes.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


Le fichier UDF peut ensuite être utilisé dans les requêtes comme dans l’exemple suivant :

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>Requête intégrés de langage JavaScript API
Outre les requêtes à l’aide de la grammaire de DocumentDB SQL, le Kit de développement côté serveur vous permet d’exécuter des requêtes optimisés à l’aide d’une interface fluent de JavaScript sans connaissance de SQL. La requête JavaScript QU'API vous permet de générer par programme des requêtes en passant fonctions prédicats dans une fonction peut être reliée appels, avec une syntaxe familier aux différencié de tableau et les bibliothèques JavaScript comme lodash de ECMAScript5. Les requêtes sont analysées par le runtime JavaScript pour être exécutées efficacement avec l’index de DocumentDB.

> [AZURE.NOTE]`__` (trait de soulignement double) est un alias pour `getContext().getCollection()`.
> <br/>
> En d’autres termes, vous pouvez utiliser `__` ou `getContext().getCollection()` pour accéder à la requête JavaScript API.

Fonctions prises en charge sont les suivantes :
<ul>
<li>
<b>Chain().... valeur ([rappel] [, options])</b>
<ul>
<li>
Démarre un appel chaîne qui doit être terminé avec value().
</li>
</ul>
</li>
<li>
<b>filtre (predicateFunction [, options] [, rappel])</b>
<ul>
<li>
Filtres de l’entrée à l’aide d’une fonction qui retourne vrai/faux afin de filtrer les documents d’entrée dans/hors dans le jeu de résultats. Cela se comporte comme une clause WHERE dans SQL.
</li>
</ul>
</li>
<li>
<b>carte (transformationFunction [, options] [, rappel])</b>
<ul>
<li>
Applique une projection donnée une fonction de transformation qui mappe chaque élément d’entrée à un objet JavaScript ou une valeur. Cela se comporte comme une clause SELECT dans SQL.
</li>
</ul>
</li>
<li>
<b>Pluck ([propertyName] [, options] [, rappel])</b>
<ul>
<li>
Il s’agit d’un raccourci pour une table qui extrait la valeur d’une propriété unique de chaque élément d’entrée.
</li>
</ul>
</li>
<li>
<b>Fusionner ([isShallow] [, options] [, rappel])</b>
<ul>
<li>
Combine et fusionne de chaque élément d’entrée dans les tableaux dans un tableau unique. Cela se comporte comme SelectMany dans LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([prédicat] [, options] [, rappel])</b>
<ul>
<li>
Produire un nouvel ensemble de documents en triant les documents dans le flux de document d’entrée dans l’ordre à l’aide de la prédicat donné croissant. Cela se comporte comme une clause ORDER BY dans SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([prédicat] [, options] [, rappel])</b>
<ul>
<li>
Produire un nouvel ensemble de documents en triant les documents dans le flux de document d’entrée dans l’ordre décroissant sous le prédicat donné. Cela se comporte comme une clause ORDER BY x DESC dans SQL.
</li>
</ul>
</li>
</ul>


Lorsque inclus dans des fonctions prédicat et/ou sélecteur, constructions JavaScript suivantes obtenir automatiquement optimisées pour s’exécuter directement sur indices DocumentDB :

* Opérateurs simples : = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Littéraux, y compris l’objet littéral : {}
* var, retour

Les constructions JavaScript suivantes ne pas obtenir optimisées pour DocumentDB indices :

* Contrôler le flux (par exemple, if, tandis que)
* Appels de fonction

Pour plus d’informations, veuillez consulter notre [JSDocs côté serveur](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exemple : Écrire une procédure stockée à l’aide de la requête JavaScript API

L’exemple de code suivant est un exemple de la façon dont l’API de requête JavaScript peut être utilisée dans le contexte d’une procédure stockée. La procédure stockée insère un document, fourni par un paramètre d’entrée et met à jour une métadonnées de document, à l’aide de la `__.filter()` méthode, avec minSize, maxSize et totalSize basé sur la propriété taille du document d’entrée.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL pour Fiche récapitulative Javascript
Le tableau suivant présente les différentes requêtes SQL et les requêtes JavaScript correspondantes.

Comme avec des requêtes SQL, clés de propriétés de document (par exemple, `doc.id`) respectent la casse.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>API de requête JavaScript</th>
<th>Plus d’informations</th>
</tr>
<tr>
<td>
<pre>
Sélectionnez * à partir de documents
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {document retour ;}) ;
</pre>
</td>
<td>Résultats dans tous les documents (paginés avec jeton de continuation) en tant qu’est.</td>
</tr>
<tr>
<td>
<pre>
Sélectionnez docs.id, docs.message en tant que message, docs.actions de documents
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {retour {id : doc.id, message : doc.message, actions : doc.actions} ;}) ;
</pre>
</td>
<td>Projets l’id, un message (alias message) et une action à partir de tous les documents.</td>
</tr>
<tr>
<td>
<pre>
Sélectionnez * à partir de documents où docs.id="X998_Y998 »
</pre>
</td>
<td>
<pre>
__.Filter(Function(doc) {renvoyer doc.id === « X998_Y998 » ;}) ;
</pre>
</td>
<td>Requêtes pour les documents comportant le prédicat : id = « X998_Y998 ».</td>
</tr>
<tr>
<td>
<pre>
Sélectionnez * à partir de documents où ARRAY_CONTAINS(docs. Indicateurs, 123)
</pre>
</td>
<td>
<pre>
__.Filter(Function(x) {renvoyer x.Tags & & x.Tags.indexOf(123) > -1 ;}) ;
</pre>
</td>
<td>Requêtes pour les documents qui ont une propriété de balises et balises est un tableau contenant la valeur 123.</td>
</tr>
<tr>
<td>
<pre>
Sélectionnez docs.id, docs.message sous forme de documents à partir de message où docs.id="X998_Y998 »
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {renvoyer doc.id === « X998_Y998 » ;}) .map(function(doc) {retour {id : doc.id, message : doc.message} ;}) .value() ;
</pre>
</td>
<td>Requêtes pour les documents comportant un prédicat, id = « X998_Y998 » et les projets puis l’id et le message (alias message).</td>
</tr>
<tr>
<td>
<pre>
Ajouter des balises de documents jointure sélectionnez valeur de la balise dans documents. Balises ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {document retour. Balises & & Array.isArray (document. Balises) ; .sortBy(function(doc)}) {retour doc._ts ;}) .pluck("tags") .flatten() .value()
</pre>
</td>
<td>Filtres pour les documents qui ont une propriété de tableau, balises, et trie les documents qui en résulte par la propriété du système DTS horodatage, puis projets + fusionne la matrice balises.</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>Prise en charge runtime
[Côté serveur DocumentDB JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) prend en charge le meilleur parti des fonctionnalités de langue JavaScript standard comme standardisé par [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Sécurité
Procédures stockées JavaScript et déclencheurs sont en mode sandbox afin que les effets d’un script ne pas renvoyer à l’autre sans passer par l’isolement transaction instantané au niveau de la base de données. Les environnements runtime sont regroupées mais nettoyés du contexte après chaque exécution. Par conséquent, ils sont considérés comme sécurisés de n’importe quel côté des effets inattendus entre eux.

### <a name="pre-compilation"></a>Compilation préalable
Procédures stockées, déclencheurs et UDF sont implicitement précompilées au format de code octet afin d’éviter le coût de la compilation au moment de chaque appel de script. Ainsi, les appels de procédures stockées sont rapides et ont un faible encombrement.

## <a name="client-sdk-support"></a>Prise en charge du Kit de développement logiciel client
Outre le client [Node.js](documentdb-sdk-node.md) , DocumentDB prend en charge [.NET](documentdb-sdk-dotnet.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/)et [Python SDK](documentdb-sdk-python.md). Procédures stockées, déclencheurs et UDF peuvent être créées et exécutées à l’aide d’un de ces SDK également. L’exemple suivant montre comment créer et exécuter une procédure stockée à l’aide du client .NET. Notez comment les types de .NET sont transmis à la procédure stockée en tant que JSON et lecture.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


Cet exemple montre comment utiliser le [Kit de développement .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) pour créer un déclencheur Pre et créer un document avec le déclencheur activé. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


Et l’exemple suivant montre comment créer une fonction définie par l’utilisateur (UDF) et l’utiliser dans une [requête SQL DocumentDB](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API REST
Toutes les opérations de DocumentDB peuvent être effectuées d’une manière RESTful. Procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur peuvent être enregistrées dans une collection de sites à l’aide de HTTP POST. Voici un exemple de l’enregistrement d’une procédure stockée :

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


La procédure stockée est enregistrée par l’exécution d’une requête POST contre l’URI bases de données/testdb/solénoïdes/testColl/procédures stockées avec le corps contenant la procédure stockée à créer. Déclencheurs et UDF peuvent être enregistrés de la même façon en envoyant un billet contre /triggers et /udfs respectivement.
Cela stocké procédure can puis être exécutée en envoyant une requête POST par rapport à son lien ressource :

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Dans ce cas, l’entrée de la procédure stockée est passée dans le corps de la requête. Notez que l’entrée est passée sous forme de tableau JSON des paramètres d’entrée. La procédure stockée accepte la première entrée sous forme d’un document qui est un corps de la réponse. La réponse que nous recevons est la suivante :

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Déclencheurs, contrairement aux procédures stockées, ne peuvent pas être exécutées directement. Au lieu de cela, elles sont exécutées dans le cadre d’une opération sur un document. Nous pouvons spécifier les déclencheurs pour exécuter avec une demande de l’aide des en-têtes HTTP. Voici la demande de création d’un document.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Ici, le déclencheur avant d’être exécuté avec la demande est spécifié dans l’en-tête x-ms-documentdb-pre-trigger-include. En conséquence, tous les déclencheurs après sont indiquées dans l’en-tête x-ms-documentdb-post-trigger-include. Notez que les deux avant et après déclencheurs peuvent être spécifiés pour une demande donnée.

## <a name="sample-code"></a>Exemples de code

Vous pouvez trouver des autres exemples de code côté serveur (y compris les [Supprimer en bloc](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js)et [mettre à jour](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) sur notre [référentiel Github](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Vous voulez partager votre procédure stockée Isard ? Envoyez-nous une demande d’extraction ! 

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez un ou plusieurs des procédures stockées, déclencheurs et les fonctions définies par l’utilisateur créées, vous pouvez les charger et les afficher dans le portail Azure à l’aide de l’Explorateur de Script. Pour plus d’informations, voir [Afficher les procédures stockées, déclencheurs et les fonctions définies par l’utilisateur à l’aide de l’Explorateur de Script DocumentDB](documentdb-view-scripts.md).

Vous pouvez également trouver utile dans votre chemin d’accès pour en savoir plus sur la programmation côté serveur DocumentDB les références et les ressources suivantes :

- [SDK DocumentDB Azure](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript – système de type JSON](http://www.json.org/js.html) 
- [Extensibilité de base de données portables et sécurisées](http://dl.acm.org/citation.cfm?id=276339) 
- [Architecture de base de données orientée service](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [Hébergement du Runtime .NET dans Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
