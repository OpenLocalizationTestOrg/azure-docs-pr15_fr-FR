<properties
    pageTitle="Prise en main stockage de tables et Visual Studio connecté services (ASP.NET) | Microsoft Azure"
    description="Comment procéder à l’aide de stockage de Table Azure dans un projet ASP.NET dans Visual Studio après vous être connecté à un compte de stockage à l’aide de Visual Studio services connectés"
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

# <a name="get-started-with-table-storage-and-visual-studio-connected-services-aspnet"></a>Prise en main stockage de tables et Visual Studio connecté services (ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble
Cet article décrit comment commencer à utiliser stockage de Table Azure dans Visual Studio une fois que vous avez créé ou référencé un compte de stockage Azure dans un projet ASP.NET à l’aide de la boîte de dialogue Visual Studio **Ajouter les Services connectés** . Cet article vous explique comment effectuer des tâches courantes dans les tables Azure, y compris la création et suppression d’une table, ainsi que pour travailler avec les entités de table. Les exemples sont écrits en C\# de code et utiliser la [Bibliothèque Microsoft Azure stockage Client pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Pour plus d’informations générales sur l’utilisation d’Azure table storage, voir [prise en main stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md).

Stockage de Table Azure vous permet de stocker de grandes quantités de données structurées. Le service est un magasin de données NoSQL qui accepte les appels authentifiés, à l’intérieur et à l’extérieur du cloud Azure. Tables Azure sont parfaites pour le stockage de données structurées et non relationnelles.


## <a name="access-tables-in-code"></a>Tables Access dans le code

1. Vérifiez que les déclarations d’espace de noms en haut du fichier c# incluent ces instructions **à l’aide** .

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenir un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Utilisez le code suivant pour obtenir la votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Remarque** - utiliser tout le code ci-dessus devant le code dans les exemples suivants.

3. Obtenir un objet **CloudTableClient** pour référencer les objets de la table dans votre compte de stockage.  

        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtenir un objet de référence **CloudTable** pour faire référence à une table spécifique et entités.

        // Get a reference to a table named "peopleTable"
        CloudTable table = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Créer une table dans le code

Pour créer la table Azure, simplement ajouter un appel à **CreateIfNotExistsAsync()** au code précédent.

    // Create the CloudTable if it does not exist
    await table.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité à une table, vous créez une classe qui définit les propriétés de votre entité. Le code suivant définit une classe d’entité appelée **CustomerEntity** qui utilise prénom du client comme la clé de ligne et le nom de famille comme clé de partition.

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

Opérations de table qui impliquent entités sont terminé à l’aide de la **CloudTable** objet que vous avez créée précédemment dans « Tables Access dans le code ». L’objet **TableOperation** représente l’opération à effectuer. L’exemple suivant montre comment créer un objet **CloudTable** et un objet **CustomerEntity** . Pour préparer l’opération, un **TableOperation** est créé pour insérer l’entité customer dans la table. Enfin, l’opération est exécutée en appelant CloudTable.ExecuteAsync.

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
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## <a name="get-a-single-entity"></a>Obtenir une seule entité
Vous pouvez écrire une requête pour obtenir une entité unique et spécifique. Le code suivant utilise un objet **TableOperation** pour spécifier un client nommé « Ben Smith ». Cette méthode retourne une entité, plutôt que d’une collection de sites et la valeur renvoyée de **TableResult.Result** est un objet **CustomerEntity** . Spécification des touches partition et de ligne dans une requête est le moyen le plus rapide pour récupérer une seule entité à partir du service de la table.

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
Vous pouvez supprimer une entité une fois que vous l’avez trouvé. Le code suivant recherche pour une entité client nommée « Ben Smith », si elle la trouve, il supprime.

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
