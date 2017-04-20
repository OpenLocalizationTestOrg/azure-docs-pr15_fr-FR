<properties
    pageTitle="Comment utiliser le stockage de tables (C++) | Microsoft Azure"
    description="Stocker des données structurées dans le cloud à l’aide de stockage de Table Azure, un magasin de données NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-table-storage-from-c"></a>Comment utiliser le stockage de Table à partir de C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble  
Ce guide vous montrent comment effectuer des scénarios courants en utilisant le service de stockage de Table Azure. Les exemples sont écrits en C++ et utilisent la [Bibliothèque de Client de stockage Azure pour C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios présentés incluent la **Création et suppression d’une table** et **l’utilisation d’entités de table**.

>[AZURE.NOTE] Ce guide cible la bibliothèque de Client de stockage Azure pour C++ version 1.0.0 et au-dessus. La version recommandée est stockage Client bibliothèque 2.2.0, qui est disponible via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>Créer une application C++  
Dans ce guide, vous allez utiliser les fonctionnalités de stockage qui peuvent être exécutées dans une application C++. Pour ce faire, vous devez installer la bibliothèque de Client de stockage Azure pour C++ et créer un compte de stockage Azure dans votre abonnement Azure.  

Pour installer la bibliothèque de Client de stockage Azure pour C++, vous pouvez utiliser les méthodes suivantes :

-   **Linux :** Suivez les instructions fournies dans la page de [Bibliothèque de Client de stockage Azure pour C++ Lisezmoi](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows :** Dans Visual Studio, cliquez sur **Outils > Gestionnaire de Package NuGet > Gestionnaire de Package Console**. Tapez la commande suivante dans la [console Gestionnaire de Package NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) , appuyez sur ENTRÉE.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Configurez votre application pour accéder au stockage de Table  
Ajoutez que les instructions vers le haut du fichier C++ où vous souhaitez utiliser le stockage Azure API pour accéder aux tableaux include suivantes :  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurer une chaîne de connexion de stockage Azure  
Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker les points de terminaison et les informations d’identification pour l’accès aux services de gestion des données. Lorsque vous exécutez une application cliente, vous devez fournir la chaîne de connexion de stockage au format suivant. Utiliser le nom de votre compte de stockage et de la touche d’accès de stockage pour le compte de stockage répertorié dans le [Portail Azure](https://portal.azure.com) pour les valeurs de *nom de compte* et *AccountKey* . Pour plus d’informations sur les comptes de stockage et les touches d’accès rapide, voir [comptes de stockage sur Azure](storage-create-storage-account.md). Cet exemple montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion :  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Pour tester votre application sur votre ordinateur local fonctionnant sous Windows, vous pouvez utiliser l' [émulateur de stockage](storage-use-emulator.md) Azure qui est installé avec le [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/). L’émulateur de stockage est un utilitaire qui simule les services Blob Azure, file d’attente et Table disponibles sur votre ordinateur de développement local. L’exemple suivant montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion à votre émulateur stockage local :  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Pour démarrer l’émulateur de stockage Azure, cliquez sur le bouton **Démarrer** ou appuyez sur la touche Windows. Commencez à taper **Émulateur de stockage Azure**, puis sélectionnez **Émulateur de stockage de Microsoft Azure** dans la liste des applications.  

Les exemples suivants part du principe que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.  

## <a name="retrieve-your-connection-string"></a>Récupérer votre chaîne de connexion  
Vous pouvez utiliser la classe **cloud_storage_account** pour représenter vos informations de compte de stockage. Pour récupérer vos informations de compte de stockage de la chaîne de connexion de stockage, vous pouvez utiliser la méthode d’analyse.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Ensuite, obtenez une référence à une classe **cloud_table_client** , car il vous permet d’obtenir des objets de référence pour les tables et entités stockées dans le service de stockage de Table. Le code suivant crée un objet **cloud_table_client** à l’aide de l’objet de compte de stockage que nous récupérées ci-dessus :  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>Créer une table
Un objet **cloud_table_client** vous permet d’obtenir des objets de référence pour les tables et entités. Le code suivant crée un objet **cloud_table_client** et utilise pour créer une nouvelle table.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table
Pour ajouter une entité à une table, créez un nouvel objet **table_entity** et passer à **table_operation::insert_entity**. Le code suivant utilise le nom du client premier en tant que la clé de ligne et le nom de famille comme clé partition. Ensemble, partition d’une entité et clé de ligne identifient l’entité de la table. Entités avec la même clé partition peuvent être interrogées plus rapidement que ceux avec touches partition différente, mais l’utilisation de touches partition centralisée permet une flexibilité opération en parallèle supérieure. Pour plus d’informations, voir [liste de vérification extensibilité élevées et les performances de stockage de Microsoft Azure](storage-performance-checklist.md).

Le code suivant crée une nouvelle instance de **table_entity** avec des données client stocké. Le code suivant appelle **table_operation::insert_entity** pour créer un objet **table_operation** pour insérer une entité dans un tableau et lui associe la nouvelle table entité. Enfin, le code appelle la méthode execute sur l’objet **cloud_table** . Et la nouvelle **table_operation** envoie une demande au service de Table pour insérer la nouvelle entité client dans la table « personnes ».  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>Insérer un lot d’entités
Vous pouvez insérer un lot d’entités au service de tableau en une seule opération d’écriture. Le code suivant crée un objet **table_batch_operation** et puis ajoute que trois opérations lui d’insertion. Chaque opération d’insertion est ajoutée en créant un nouvel objet entité, définissant ses valeurs et en appelant ensuite la méthode insertion sur l’objet **table_batch_operation** pour associer l’entité à une opération d’insertion. Ensuite, **cloud_table.execute** est appelée pour exécuter l’opération.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Quelques éléments à noter sur les opérations de commandes :  

-   Vous pouvez effectuer jusqu'à 100 insérer, supprimer, fusionner, remplacer, opérations de fusion insertion ou et insertion ou remplacer dans n’importe quelle combinaison dans un seul lot.  
-   Une opération par lots peut avoir une opération récupérer, s’il s’agit de la seule opération dans le lot.  
-   Toutes les entités dans une seule opération doivent avoir la même clé partition.  
-   Une opération par lots est limitée à une charge 4 Mo de données.  

## <a name="retrieve-all-entities-in-a-partition"></a>Récupérer toutes les entités dans une partition
Pour interroger un tableau pour toutes les entités dans une partition, utilisez un objet **table_query** . L’exemple suivant spécifie un filtre pour les entités où « Smith » est la clé de partition. Cet exemple imprime les champs de chaque entité dans les résultats de requête à la console.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

La requête dans cet exemple affiche toutes les entités qui correspondent aux critères de filtre. Si vous avez grands tableaux et devons pour télécharger les entités de table souvent, nous vous recommandons stockés vos données dans des objets BLOB Azure stockage à la place.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Récupérer une plage d’entités dans une partition
Si vous ne voulez pas toutes les entités dans une partition de la requête, vous pouvez spécifier une plage en combinant le filtre clé partition avec un filtre de clés de ligne. L’exemple suivant utilise deux filtres pour obtenir toutes les entités dans partition 'Smith' où la clé de ligne (prénom) commence par une lettre « E » dans l’alphabet au plus tôt et imprime ensuite les résultats de la requête.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>Récupérer une seule entité
Vous pouvez écrire une requête pour récupérer une entité unique et spécifique. Le code suivant utilise **table_operation::retrieve_entity** pour spécifier le client « Jeff Smith ». Cette méthode retourne une entité, plutôt qu’une collection de sites, et la valeur retournée est dans **table_result**. Spécification des touches partition et de ligne dans une requête est le moyen le plus rapide pour récupérer une seule entité à partir du service de la Table.  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>Remplacer une entité
Pour remplacer une entité, extraire à partir du service de Table, modifier l’objet entité, puis enregistrez les modifications dans le service de tableau. Le code suivant modifie d’un client existant téléphone numéro et l’adresse e-mail. Au lieu d’appeler **table_operation::insert_entity**, ce code utilise **table_operation::replace_entity**. Cela provoque l’entité à remplacer entièrement sur le serveur, sauf si l’entité sur le serveur a changé dans la mesure où il a été récupéré, auquel cas l’opération échouera. Ce problème consiste à empêcher votre application remplacer par inadvertance une modification apportée par un autre composant de votre application entre l’extraction et la mise à jour. Au bon déroulement de ce problème consiste à récupérer l’entité à nouveau, apportez vos modifications (s’il est toujours valide), puis effectuez une autre opération **table_operation::replace_entity** . La section suivante vous montrent comment substituer ce comportement.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>Insérer ou remplacer une entité
opérations **table_operation::replace_entity** échouera si l’entité a été modifiée dans la mesure où il a été récupéré à partir du serveur. Par ailleurs, vous devez récupérer l’entité à partir du serveur tout d’abord dans l’ordre de **table_operation::replace_entity** aboutisse. Parfois, cependant, vous ne savez pas si l’entité existe sur le serveur et les valeurs actuelles stockées qu’elle ne sont pas appliquées — votre mise à jour doit remplacer tous les. Pour ce faire, vous utiliserez une opération **table_operation::insert_or_replace_entity** . Cette opération insère l’entité s’il n’existe pas, ou remplace l’affirmative, quelle que soit la quand la dernière mise à jour ont été apportée. Dans l’exemple suivant, l’entité customer pour Jeff Smith est toujours récupérée, mais il est ensuite enregistrée sur le serveur via **table_operation::insert_or_replace_entity**. Les mises à jour apportées à l’entité entre l’extraction et l’opération de mise à jour seront remplacées.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête  
Une requête à une table pouvez récupérer quelques propriétés d’une entité. La requête dans le code suivant utilise la méthode **table_query::set_select_columns** pour renvoyer uniquement les adresses de messagerie d’entités dans le tableau.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] Certaines propriétés d’une entité l’interrogation sont une opération plus efficace que la récupération de toutes les propriétés.

## <a name="delete-an-entity"></a>Supprimer une entité
Vous pouvez facilement supprimer une entité une fois que vous l’avez extrait. Une fois que l’entité est extrait, appelez **table_operation::delete_entity** avec l’entité à supprimer. Puis appelez la méthode **cloud_table.execute** . Le code suivant extrait et supprime une entité avec une clé de partition de « Toile » et une clé de ligne de « Jeff ».  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>Supprimer un tableau
Enfin, l’exemple suivant supprime un tableau à partir d’un compte de stockage. Une table qui a été supprimée n’est pas disponible pour être recréée pour une période de temps après la suppression.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez appris les notions de base de stockage de tables, cliquez sur ces liens pour en savoir plus sur le stockage Azure :  

-   [L’utilisation de stockage d’objets Blob à partir de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Comment utiliser le stockage de file d’attente à partir de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Liste des ressources de stockage Azure en C++](storage-c-plus-plus-enumeration.md)
-   [Bibliothèque de Client de stockage pour la référence C++](http://azure.github.io/azure-storage-cpp)
-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
