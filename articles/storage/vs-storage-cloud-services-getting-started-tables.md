<properties
    pageTitle="Prise en main stockage de tables et Visual Studio connecté services (services cloud) | Microsoft Azure"
    description="Comment procéder à l’aide de stockage de Table Azure dans un projet de service cloud dans Visual Studio après vous être connecté à un compte de stockage à l’aide de Visual Studio services connectés"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Prise en main de stockage de table Azure et Visual Studio connecté services (cloud services projets)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

##<a name="overview"></a>Vue d’ensemble

Cet article décrit comment commencer à utiliser stockage de table Azure dans Visual Studio une fois que vous avez créé ou référencé un compte de stockage Azure dans un projet de services cloud à l’aide de la boîte de dialogue Visual Studio **Ajouter les Services connectés** . L’opération **d’Ajout de Services connectés** installe les packages NuGet appropriées pour accéder au stockage Azure dans votre projet et ajoute la chaîne de connexion pour le compte de stockage pour vos fichiers de configuration de projet.

Le service de stockage de Table Azure vous permet de stocker de grandes quantités de données structurées. Le service est un magasin de données NoSQL qui accepte les appels authentifiés, à l’intérieur et à l’extérieur du cloud Azure. Tables Azure sont parfaites pour le stockage de données structurées et non relationnelles.

Pour commencer, vous devez tout d’abord créer une table dans votre compte de stockage. Nous allons vous montrer comment créer une table Azure dans le code, mais également comment effectuer des opérations d’entité, telles que l’ajout, la modification, la lecture et la lecture entités de table et de tableau de base. Les exemples sont écrits en C\# de code et utiliser la [bibliothèque du client Microsoft Azure stockage pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Remarque :** Certaines des API effectuées appels arrière vers le stockage Azure sont asynchrones. Pour plus d’informations, voir [la programmation asynchrone avec asynchrone et Await](http://msdn.microsoft.com/library/hh191443.aspx) . Le code ci-dessous suppose asynchrone des méthodes de programmation sont utilisés.

- Pour plus d’informations sur manipuler des tables, voir [prise en main stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md) .
- Consultez la [documentation du stockage](https://azure.microsoft.com/documentation/services/storage/) des informations générales sur le stockage Azure.
- Voir [la documentation de Services de Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) pour des informations générales sur les services en nuage Azure.
- Pour plus d’informations sur la programmation d’applications ASP.NET, consultez [ASP.NET](http://www.asp.net) .

## <a name="access-tables-in-code"></a>Tables Access dans le code

Pour accéder aux tables dans les projets de service cloud, vous devez inclure les éléments suivants à des fichiers de source c# accéder au stockage de table Azure.

1. Vérifiez que les déclarations d’espace de noms en haut du fichier c# incluent ces instructions **à l’aide** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenir un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Utilisez le code suivant pour obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
> [AZURE.NOTE]  Utiliser tout le code ci-dessus devant le code dans les exemples suivants.

3. Obtenir un objet **CloudTableClient** pour référencer les objets de la table dans votre compte de stockage.

         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtenir un objet de référence **CloudTable** pour faire référence à une table spécifique et entités.

        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Créer une table dans le code

Pour créer la table Azure, il suffit d’ajouter un appel à **CreateIfNotExistsAsync** à la fois que vous obtenez un objet **CloudTable** comme décrit dans la section « Accéder aux tables dans le code ».

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité à une table, créez une classe qui définit les propriétés de votre entité. Le code suivant définit une classe d’entité appelée **CustomerEntity** qui utilise le nom du client premier comme la clé de ligne et le nom comme clé de partition.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Opérations de table qui impliquent entités sont effectuées à l’aide de l’objet **CloudTable** que vous avez créée précédemment dans « accéder aux tables dans le code. » L’objet **TableOperation** représente l’opération à effectuer. L’exemple suivant montre comment créer un objet **CloudTable** et un objet **CustomerEntity** . Pour préparer l’opération, un **TableOperation** est créé pour insérer l’entité customer dans la table. Enfin, l’opération est exécutée en appelant **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Insérer un lot d’entités

Vous pouvez insérer plusieurs entités dans un tableau dans une opération d’écriture unique. L’exemple suivant crée deux objets entité (« Jeff Smith » et « Ben Smith »), les ajoute à un objet **TableBatchOperation** à l’aide de la méthode d’insertion et puis démarre l’opération en appelant **CloudTable.ExecuteBatchAsync**.

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Intégrer l’ensemble des entités dans une partition

Pour interroger un tableau pour toutes les entités dans une partition, utilisez un objet **TableQuery** . L’exemple suivant spécifie un filtre pour les entités où « Smith » est la clé de partition. Cet exemple imprime les champs de chaque entité dans les résultats de requête à la console.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## <a name="get-a-single-entity"></a>Obtenir une seule entité

Vous pouvez écrire une requête pour obtenir une entité unique et spécifique. Le code suivant utilise un objet **TableOperation** pour spécifier un client nommé « Ben Smith ». Cette méthode retourne une entité, plutôt que d’une collection de sites et la valeur renvoyée de **TableResult.Result** est un objet **CustomerEntity** . Spécification des touches partition et de ligne dans une requête est le moyen le plus rapide pour récupérer une seule entité à partir du service de la **Table** .

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Supprimer une entité
Vous pouvez supprimer une entité une fois que vous l’avez trouvé. Le code suivant recherche pour une entité client nommée « Jean Philippe toile », et si elle la trouve, il supprime.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
