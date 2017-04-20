<properties
   pageTitle="Développement avec plusieurs régions avec DocumentDB | Microsoft Azure"
   description="Découvrez comment accéder à vos données dans plusieurs zones d’Azure DocumentDB, un service de base de données NoSQL entièrement géré."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>Développement avec les comptes DocumentDB plusieurs régions

> [AZURE.NOTE] Distribution globales des bases de données DocumentDB est généralement disponible et automatiquement activée pour tous les comptes DocumentDB nouvellement créés. Nous travaillons pour activer la distribution globale sur tous les comptes existants, mais en attendant, si vous souhaitez que la distribution globale activée pour votre compte, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et nous allons activez-le pour vous maintenant.

Pour tirer parti de [distribution globale](documentdb-distribute-data-globally.md), les applications clientes peuvent spécifier la liste de préférence ordonnée des régions à utiliser pour effectuer des opérations de document. Pour ce faire, vous pouvez définir la stratégie de connexion. En fonction de la configuration du compte Azure DocumentDB, disponibilité régionale actuelle et la liste de préférence spécifié, le point de terminaison optimale est sélectionnée par le Kit de développement pour effectuer d’écriture et les opérations de lecture. 

Cette liste de préférence est spécifiée lors de l’initialisation d’une connexion en utilisant le client DocumentDB SDK. Kits de développement logiciel acceptent un paramètre facultatif « PreferredLocations » qui est une liste des régions Azure.

Le Kit de développement envoie automatiquement toutes les écritures en cours écrire région. 

Toutes les lectures seront envoyées à la première zone disponible dans la liste PreferredLocations. Si la requête échoue, le client échouer vers le bas de la liste à la zone suivante et ainsi de suite. 

Le client de que SDK uniquement tentera lire les régions PreferredLocations spécifiées dans. Par conséquent, par exemple, si le compte de base de données est disponible dans trois régions, mais le client spécifie uniquement deux des régions non écriture pour PreferredLocations, puis aucune lecture n’est fournis sortir de la région d’écriture, même en cas de basculement.

L’application peut vérifier le point de terminaison écriture actuel et lire le point de terminaison choisie par le Kit de développement en vérifiant deux propriétés, WriteEndpoint et ReadEndpoint, disponible dans la version SDK 1.8 et versions ultérieures. 

Si la propriété PreferredLocations n’est pas définie, toutes les demandes seront servis à partir de la zone d’écriture en cours. 


## <a name="net-sdk"></a>KIT DE DÉVELOPPEMENT .NET
Le Kit de développement peut être utilisé sans modifier le code. Dans ce cas, le Kit de développement redirige les deux opérations de lecture automatiquement et écrit dans la zone d’écriture en cours. 

Dans la version 1,8 et version ultérieure du Kit de développement .NET, le paramètre ConnectionPolicy pour le constructeur DocumentClient a une propriété appelée Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Cette propriété est de type Collection `<string>` et doit contenir une liste de noms de région. Les valeurs de chaîne sont mis en forme par la colonne nom de la région sur les [Régions Azure]  [ regions] page, sans espace avant ou après le premier et dernier caractère respectivement.

L’écriture en cours et les points de terminaison de lecture sont disponibles dans DocumentClient.WriteEndpoint et DocumentClient.ReadEndpoint respectivement.

> [AZURE.NOTE] Les URL pour les points de terminaison ne doivent pas être considérées comme des constantes à long terme. Le service peut mettre à jour ces à tout moment. Le Kit de développement gère automatiquement cette modification.

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript et Python SDK
Le Kit de développement peut être utilisé sans modifier le code. Dans ce cas, le Kit de développement dirigera automatiquement lecture et écriture vers la version écrire région. 

Dans la version 1,8 et versions ultérieure de chaque kit de développement logiciel, le paramètre ConnectionPolicy pour le constructeur DocumentClient une nouvelle propriété appelée DocumentClient.ConnectionPolicy.PreferredLocations. Ce paramètre est un tableau de chaînes qui prend une liste de noms de région. Les noms sont mis en forme par la colonne nom de la région dans les [Régions Azure]  [ regions] page. Vous pouvez également utiliser des constantes prédéfinies dans l’objet commodité AzureDocuments.Regions

L’écriture en cours et les points de terminaison de lecture sont disponibles dans DocumentClient.getWriteEndpoint et DocumentClient.getReadEndpoint respectivement.

> [AZURE.NOTE] Les URL pour les points de terminaison ne doivent pas être considérées comme des constantes à long terme. Le service peut mettre à jour ces à tout moment. Le Kit de développement gérera automatiquement cette modification.

Voici un exemple de code pour NodeJS/Javascript. Python et Java suit le même modèle.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>RESTE 
Une fois qu’un compte de base de données a été créé dans plusieurs régions, les clients peuvent interroger sa disponibilité en effectuant une requête GET sur l’URI suivant.

    https://{databaseaccount}.documents.azure.com/

Le service renverra une liste des régions et leur DocumentDB point de terminaison correspondant MU pour les réplicas. La zone d’écriture en cours sera indiquée dans la réponse. Le client peut ensuite sélectionner le point de terminaison approprié pour toutes les autres API REST demandes comme suit.

Réponse d’exemple

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   Demandes de toutes les placer, publier et supprimer doivent atteindre l’écriture indiquée URI
-   Obtient tous les et autres requêtes en lecture seule (par exemple les requêtes) peuvent accéder à un point de terminaison de choix du client

Écrire des requêtes en lecture seule parties échouera avec code d’erreur HTTP 403 (« refusé »).

Si la zone écriture modifié après la phase de découverte initiale du client, écriture ultérieure à la région précédente écriture échouera avec code d’erreur HTTP 403 (« refusé »). Le client doit ensuite obtenir la liste des régions à nouveau pour obtenir la région écriture mis à jour.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les données de distribution globalement avec DocumentDB dans les articles suivants :

- [Distribuer des données globalement avec DocumentDB](documentdb-distribute-data-globally.md)
- [Niveaux de cohérence](documentdb-consistency-levels.md)
- [Fonctionne de débit avec plusieurs régions](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [Ajouter des zones à l’aide du portail Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
