<properties
    pageTitle="Didacticiel NoSQL : Kit de développement .NET DocumentDB | Microsoft Azure"
    description="Didacticiel NoSQL qui crée une base de données en ligne et l’application console c# en utilisant le Kit de développement .NET DocumentDB. DocumentDB est une base de données NoSQL JSON."
    keywords="didacticiel NoSQL, base de données en ligne, application console c#"
    services="documentdb"
    documentationCenter=".net"
    authors="AndrewHoh"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/29/2016"
    ms.author="anhoh"/>

# <a name="nosql-tutorial-build-a-documentdb-c-console-application"></a>NoSQL didacticiel : créer une DocumentDB c# application console

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenue dans le didacticiel NoSQL pour le Kit de développement Azure DocumentDB .NET ! Après avoir suivi ce didacticiel, vous avez une application console qui crée et requêtes DocumentDB ressources.

Nous aborderons :

- Création et la connexion à un compte DocumentDB
- Configuration de votre Solution Visual Studio
- Création d’une base de données en ligne
- Création d’une collection de sites
- Création de documents JSON
- Interroger la collection de sites
- Remplacement d’un document
- Suppression d’un document
- Suppression de la base de données

Ne pas avoir le temps ? Ne vous inquiétez pas ! Une solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Accéder à [obtenir la section solution complète](#GetSolution) pour obtenir des instructions rapides.

Ensuite, utilisez les boutons de vote en haut ou en bas de cette page pour nous envoyer des commentaires. Si vous souhaitez que nous vous contacter directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Maintenant prise en main !

## <a name="prerequisites"></a>Conditions préalables

Vérifiez que vous disposez des éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).
- [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com/).
- .NET framework 4.6

## <a name="step-1-create-a-documentdb-account"></a>Étape 1 : Créer un compte DocumentDB

Créer un compte DocumentDB. Si vous possédez déjà un compte que vous souhaitez utiliser, vous pouvez passer à [configurer votre Solution Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>Étape 2 : Configurez votre solution Visual Studio

1. Ouvrir **Visual Studio 2015** sur votre ordinateur.
2. Dans le menu **fichier** , sélectionnez **Nouveau**, puis **projet**.
3. Dans la boîte de dialogue **Nouveau projet** , sélectionnez **modèles** / **c#** / **Application Console**, nommez votre projet, puis cliquez sur **OK**.
![Capture d’écran de la fenêtre Nouveau projet](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. Dans l' **Explorateur de solutions**, cliquez avec l’avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio.
5. Puis sans quitter le menu, cliquez sur **Gérer les Packages NuGet...** 
 ![Capture du Menu Clicked droite pour le projet](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. Dans l’onglet **Nuget** , cliquez sur **Parcourir**, puis tapez **documentdb azure** dans la zone de recherche.
7. Dans les résultats, recherchez **Microsoft.Azure.DocumentDB** et cliquez sur **installer**.
L’ID de package de la bibliothèque de Client DocumentDB est [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)
![capture du Nuget Menu pour la recherche DocumentDB Client SDK](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

Rédaction ! Maintenant que nous avons terminé la configuration, commençons écrire du code. Vous pouvez trouver un projet code complété de ce didacticiel sur [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Étape 3 : Se connecter à un compte DocumentDB

Tout d’abord, ajoutez ces références au début de votre application c#, dans le fichier Program.cs :

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel NoSQL, assurez-vous que vous ajoutez les dépendances ci-dessus.

À présent, ajoutez ces deux constantes et votre variable *client* en dessous de votre *programme*de cours public.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

Ensuite, tête au [Portail Azure](https://portal.azure.com) pour récupérer votre URI et la clé primaire. Le DocumentDB URI et la clé primaire sont nécessaires pour votre application pour déterminer où se connecter et DocumentDB d’approuver la connexion de votre application.

Dans le portail Azure, accédez à votre compte DocumentDB, puis cliquez sur **les clés**.

Copier l’URI à partir du portail et collez-le dans `<your endpoint URI>` dans le fichier program.cs. Copier la clé primaire à partir du portail, puis collez-le dans `<your key>`.

![Capture d’écran du portail Azure utilisé par le didacticiel NoSQL pour créer une application console c#. Affiche un compte DocumentDB, avec le hub ACTIVE mis en surbrillance, le bouton touches mis en évidence sur la carte de compte DocumentDB et les valeurs URI, clé primaire et clé secondaire mis en évidence sur la carte de clés][keys]

Nous allons commencer l’application de mise en route lors de l’obtention en créant une nouvelle instance de la **DocumentClient**.

Sous la méthode **Main** , ajoutez cette nouvelle tâche asynchrone appelée **GetStartedDemo**, qui sera instanciation notre nouveau **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
    }

Ajoutez le code suivant pour exécuter votre tâche asynchrone à partir de votre méthode **Main** . La méthode **Main** sera intercepter des exceptions et les enregistrer dans la console.

    static void Main(string[] args)
    {
            // ADD THIS PART TO YOUR CODE
            try
            {
                    Program p = new Program();
                    p.GetStartedDemo().Wait();
            }
            catch (DocumentClientException de)
            {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
            }
            catch (Exception e)
            {
                    Exception baseException = e.GetBaseException();
                    Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
            }
            finally
            {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
            }

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez réussi à connecter à un compte DocumentDB, nous allons maintenant Examinons utilisation des ressources DocumentDB.  

## <a name="step-4-create-a-database"></a>Étape 4 : Créer une base de données
Avant d’ajouter le code pour la création d’une base de données, ajoutez une méthode d’assistance pour l’écriture dans la console.

Copiez et collez la méthode **WriteToConsoleAndPromptToContinue** en dessous de la méthode **GetStartedDemo** .

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Votre DocumentDB [base de données](documentdb-resources.md#databases) peut être créés à l’aide de la méthode [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la classe **DocumentClient** . Une base de données est le conteneur logique du stockage des documents JSON partitionnée provenant de plusieurs collections.

Copiez et collez la méthode **CreateDatabaseIfNotExists** en dessous de la méthode **WriteToConsoleAndPromptToContinue** .

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDatabaseIfNotExists(string databaseName)
    {
            // Check to verify a database with the id=FamilyDB does not exist
            try
            {
                    await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(databaseName));
                    this.WriteToConsoleAndPromptToContinue("Found {0}", databaseName);
            }
            catch (DocumentClientException de)
            {
                    // If the database does not exist, create a new database
                    if (de.StatusCode == HttpStatusCode.NotFound)
                    {
                            await this.client.CreateDatabaseAsync(new Database { Id = databaseName });
                            this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
                    }
                    else
                    {
                            throw;
                    }
            }
    }

Copiez et collez le code suivant à votre méthode **GetStartedDemo** en dessous de la création de client. Cela crée une base de données nommée *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez créé une base de données DocumentDB.  

## <a id="CreateColl"></a>Étape 5 : Créer une collection de sites  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** crée une nouvelle collection avec débit réservé, qui applique des tarifs implications. Pour plus d’informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).

Une [collection de sites](documentdb-resources.md#collections) peut être créés à l’aide de la méthode [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la classe **DocumentClient** . Une collection de sites est un conteneur de documents JSON et logique de l’application JavaScript associée.

Copiez et collez la méthode **CreateDocumentCollectionIfNotExists** en dessous de votre méthode **CreateDatabaseIfNotExists** .

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDocumentCollectionIfNotExists(string databaseName, string collectionName)
    {
        try
        {
            await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
            this.WriteToConsoleAndPromptToContinue("Found {0}", collectionName);
        }
        catch (DocumentClientException de)
        {
            // If the document collection does not exist, create a new collection
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                DocumentCollection collectionInfo = new DocumentCollection();
                collectionInfo.Id = collectionName;

                // Configure collections for maximum query flexibility including string range queries.
                collectionInfo.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

                // Here we create a collection with 400 RU/s.
                await this.client.CreateDocumentCollectionAsync(
                    UriFactory.CreateDatabaseUri(databaseName),
                    collectionInfo,
                    new RequestOptions { OfferThroughput = 400 });

                this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
            }
            else
            {
                throw;
            }
        }
    }

Copiez et collez le code suivant à votre méthode **GetStartedDemo** en dessous de la création d’une base de données. Cela crée une collection de document nommée *FamilyCollection_oa*.

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez créé une collection de documents DocumentDB.  

## <a id="CreateDoc"></a>Étape 6 : Créer des documents JSON
Un [document](documentdb-resources.md#documents) peut être créé à l’aide de la méthode [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la classe **DocumentClient** . Les documents sont définies par l’utilisateur du contenu JSON (arbitraire). Nous pouvons à présent insérer un ou plusieurs documents. Si vous avez déjà des données que vous voulez stocker dans votre base de données, vous pouvez utiliser de DocumentDB [l’outil de Migration de données](documentdb-import-data.md).

Tout d’abord, nous avons besoin créer une classe **famille** représentant des objets stockés dans DocumentDB dans cet exemple. Nous allons créer également **Parent**, **enfant**, **compagnie**, sous-classes **adresse** qui sont utilisés au sein de **famille**. Notez que les documents doivent avoir une propriété **Id** sérialisée en tant qu' **identifiant** dans JSON. Créer ces classes en ajoutant les classes sous-adresse internes suivantes après la méthode **GetStartedDemo** .

Copiez et collez la **famille**, **Parent**, **enfant**, **compagnie**et classes **d’adresses** en dessous de la méthode **WriteToConsoleAndPromptToContinue** .

    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }

    // ADD THIS PART TO YOUR CODE
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
                return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

Copiez et collez la méthode **CreateFamilyDocumentIfNotExists** en dessous de votre méthode **CreateDocumentCollectionIfNotExists** .

    // ADD THIS PART TO YOUR CODE
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
            }
            else
            {
                throw;
            }
        }
    }

Et insérez deux documents, une pour la famille Andersen et de la famille Wakefield.

Copiez et collez le code suivant à votre méthode **GetStartedDemo** en dessous de la création de collections de document.

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

    await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    Family andersenFamily = new Family
    {
            Id = "Andersen.1",
            LastName = "Andersen",
            Parents = new Parent[]
            {
                    new Parent { FirstName = "Thomas" },
                    new Parent { FirstName = "Mary Kay" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FirstName = "Henriette Thaulow",
                            Gender = "female",
                            Grade = 5,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Fluffy" }
                            }
                    }
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

    Family wakefieldFamily = new Family
    {
            Id = "Wakefield.7",
            LastName = "Wakefield",
            Parents = new Parent[]
            {
                    new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                    new Parent { FamilyName = "Miller", FirstName = "Ben" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FamilyName = "Merriam",
                            FirstName = "Jesse",
                            Gender = "female",
                            Grade = 8,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Goofy" },
                                    new Pet { GivenName = "Shadow" }
                            }
                    },
                    new Child
                    {
                            FamilyName = "Miller",
                            FirstName = "Lisa",
                            Gender = "female",
                            Grade = 1
                    }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez créé deux documents DocumentDB.  

![Diagramme illustrant la relation hiérarchique entre le compte, la base de données en ligne, la collection de sites et les documents utilisés par le didacticiel NoSQL pour créer une application console c#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Étape 7 : Interroger DocumentDB ressources

DocumentDB prend en charge des [requêtes](documentdb-sql-query.md) enrichies sur JSON documents stockés dans chaque collection de sites.  Le code suivant montre différentes requêtes - à l’aide de la syntaxe SQL DocumentDB ainsi que LINQ - que nous pouvons exécuter sur les documents nous inséré dans l’étape précédente.

Copiez et collez la méthode **ExecuteSimpleQuery** en dessous de votre méthode **CreateFamilyDocumentIfNotExists** .

    // ADD THIS PART TO YOUR CODE
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

            // Here we find the Andersen family via its LastName
            IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                    .Where(f => f.LastName == "Andersen");

            // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
            Console.WriteLine("Running LINQ query...");
            foreach (Family family in familyQuery)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            // Now execute the same query via direct SQL
            IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                    "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                    queryOptions);

            Console.WriteLine("Running direct SQL query...");
            foreach (Family family in familyQueryInSql)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Copiez et collez le code suivant à votre méthode **GetStartedDemo** en dessous de la deuxième création de documents.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez correctement interrogé par rapport à une collection DocumentDB.

Le diagramme suivant illustre comment le code SQL DocumentDB syntaxe de requête est appelée par rapport à la collection de sites que vous avez créé et la même logique s’applique à la requête LINQ également.

![Diagramme illustrant la portée et ce qui signifie que de la requête utilisée par le didacticiel NoSQL pour créer une application console c#](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

Le mot-clé [FROM](documentdb-sql-query.md#from-clause) est facultatif dans la requête, car les requêtes de DocumentDB sont déjà associées à une collection unique. Par conséquent, « De familles f » peut être remplacée par « À partir de racine r », ou toute autre variable nom que vous choisissez. DocumentDB sera déduire cette familles, racine ou le nom de la variable que vous avez choisi, faire référence à la collection actuelle par défaut.

##<a id="ReplaceDocument"></a>Étape 8 : Remplacez document JSON

DocumentDB prend en charge le remplacement documents JSON.  

Copiez et collez la méthode **ReplaceFamilyDocument** en dessous de votre méthode **ExecuteSimpleQuery** .

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
        try
        {
            await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
            this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Copiez et collez le code suivant à votre méthode **GetStartedDemo** en dessous de l’exécution des requêtes. Une fois le document, la requête même à nouveau pour afficher le document modifié est exécutée.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez correctement remplacé un document DocumentDB.

##<a id="DeleteDocument"></a>Étape 9 : Suppression d’un document JSON

DocumentDB prend en charge les documents JSON suppression.  

Copiez et collez la méthode **DeleteFamilyDocument** en dessous de votre méthode **ReplaceFamilyDocument** .

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        try
        {
            await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
            Console.WriteLine("Deleted Family {0}", documentName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Copiez et collez le code suivant à votre méthode **GetStartedDemo** en dessous de l’exécution de la deuxième requête.

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez supprimé un document DocumentDB.

##<a id="DeleteDatabase"></a>Étape 10 : Supprimer la base de données

La base de données supprimer la base de données et toutes les ressources enfants (collections de sites, documents, etc.).

Copiez et collez le code suivant à votre méthode **GetStartedDemo** sous le document supprimer pour supprimer la base de données entière et toutes les ressources enfants.

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));

Appuyez sur **F5** pour exécuter votre application.

Félicitations ! Vous avez supprimé une base de données DocumentDB.

##<a id="Run"></a>Étape 11 : Exécuter votre application console c# ensemble !

Appuyez sur F5 dans Visual Studio pour créer l’application en mode débogage.

Vous devriez voir le résultat de votre application de mise en route get. Le résultat affiche les résultats des requêtes nous avons ajouté et doit correspondre le texte d’exemple ci-dessous.

    Created FamilyDB_oa
    Press any key to continue ...
    Created FamilyCollection_oa
    Press any key to continue ...
    Created Family Andersen.1
    Press any key to continue ...
    Created Family Wakefield.7
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Replaced Family Andersen.1
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Deleted Family Andersen.1
    End of demo, press any key to exit.

Félicitations ! Vous avez terminé ce didacticiel NoSQL et que vous avez une application de console c# de travail !

##<a id="GetSolution"></a>Obtenir la solution didacticiel NoSQL complète
Pour créer la solution GetStarted qui contient tous les exemples de cet article, vous devez les éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).
-   Un [compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) disponible sur GitHub.

Pour restaurer les références aux DocumentDB .NET SDK dans Visual Studio, avec le bouton droit de la solution **GetStarted** dans l’Explorateur, puis cliquez sur **Activer NuGet Package restaurer**. Ensuite, dans le fichier App.config, mettre à jour les valeurs EndpointUrl et AuthorizationKey conformément à [se connecter à un compte DocumentDB](#Connect).

## <a name="next-steps"></a>Étapes suivantes

- Vous voulez un didacticiel ASP.NET MVC NoSQL plus complexe ? Voir [créer une application web avec ASP.NET MVC à l’aide de DocumentDB](documentdb-dotnet-application.md).
- Vous voulez effectuer des tests de performances avec DocumentDB et échelle ? Consultez [performances et échelle réalisés avec DocumentDB Azure](documentdb-performance-testing.md)
-   Découvrez comment [un compte DocumentDB moniteur](documentdb-monitor-accounts.md).
-   Exécuter des requêtes sur notre exemple de dataset dans le [Laboratoire de requête](https://www.documentdb.com/sql/demo).
-   En savoir plus sur le modèle de programmation dans la section développer de la [page de la documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
