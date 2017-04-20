<properties
    pageTitle="Comment utiliser le stockage de table à partir de PHP | Microsoft Azure"
    description="Découvrez comment utiliser le service de tableau à partir de PHP pour créer et supprimer un tableau et insérer, supprimer et interroger la table."
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>Comment utiliser le stockage de table à partir de PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous montre comment effectuer des scénarios courants au moyen du service de Table Azure. Les exemples sont écrits en PHP et utiliser le [Kit de développement logiciel Azure pour PHP][download]. Les scénarios présentés incluent la **Création et suppression d’une table et insertion, suppression et l’interrogation entités dans un tableau**. Pour plus d’informations sur le service de Table Azure, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Créer une application PHP

La seule exigence pour la création d’une application PHP qui accède au service de Table Azure est la référence de classes dans le Kit de développement Azure pour PHP à partir d’au sein de votre code. Vous pouvez utiliser les outils de développement pour créer votre application, y compris le bloc-notes.

Dans ce guide, vous utilisez les fonctionnalités de service de tableau qui peuvent être appelées à partir d’une application PHP localement, ou dans le code en cours d’exécution dans un rôle web Azure, un rôle de travail ou un site Web.

## <a name="get-the-azure-client-libraries"></a>Obtenir les bibliothèques Client Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurez votre application pour accéder au service de Table

Pour utiliser le service de Table Azure API, vous devez :

1. Faire référence au fichier chargeur automatique à l’aide de la [require_once] [ require_once] instruction, et
2. Faire référence à toutes les classes que vous utilisez.

L’exemple suivant montre comment inclure le fichier chargeur automatique et faire référence à la classe **ServicesBuilder** .

> [AZURE.NOTE] Cet exemple montre comment (et autres exemples de cet article) part du principe que vous avez installé les bibliothèques du Client PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement, vous devez faire référence à la <code>WindowsAzure.php</code> fichier chargeur automatique.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Dans les exemples ci-dessous, la `require_once` instruction est toujours affichée, mais uniquement les classes nécessaires à l’exemple s’exécute sont référencées.

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Pour instanciation un client de service de Table Azure, vous devez tout d’abord une chaîne de connexion valide. Le format de la chaîne de connexion de service de Table est la suivante :

Pour accéder à un service live :

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Pour accéder à l’espace de stockage émulateur :

    UseDevelopmentStorage=true


Pour créer un client Azure service, vous devez utiliser la classe **ServicesBuilder** . Vous pouvez :

* passer la chaîne de connexion directement ou
* utiliser le **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    * par défaut, il est fourni avec prise en charge pour une source externe - variables d’environnement
    * Vous pouvez ajouter de nouvelles sources en étendant la classe **ConnectionStringSource**

Pour les exemples présentés ici, la chaîne de connexion est passée directement.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>Créer une table

Un objet **TableRestProxy** vous permet de créer un tableau avec la méthode **Create table** . Lorsque vous créez une table, vous pouvez définir le délai d’expiration du service de tableau. (Pour plus d’informations sur le délai d’expiration du service de Table, consultez [Définition de délais pour les opérations de Service Table][table-service-timeouts].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

Pour plus d’informations sur les restrictions sur les noms des tables, voir [Présentation du modèle de données de Service Table][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité à une table, créez un nouvel objet **entité** et passer à **TableRestProxy -> insertEntity**. Notez que lorsque vous créez une entité, vous devez spécifier une `PartitionKey` et `RowKey`. Ce sont les identificateurs uniques pour une entité et sont des valeurs qui peuvent être interrogées beaucoup plus rapidement que d’autres propriétés de l’entité. Le système utilise `PartitionKey` automatiquement distribuer entités du tableau sur grand nombre de nœuds de stockage. Entités avec le même `PartitionKey` sont stockées sur le même nœud. (Effectuent des opérations sur plusieurs entités stockées sur le même nœud supérieure aux entités stockées sur différents nœuds.) La `RowKey` est l’ID unique d’une entité au sein d’une partition.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Pour plus d’informations sur les types et les propriétés du tableau, voir [Présentation du modèle de données de Service Table][table-data-model].

La classe **TableRestProxy** propose deux autres méthodes pour insérer des entités : **insertOrMergeEntity** et **insertOrReplaceEntity**. Pour utiliser ces méthodes, créer une **entité** et passer en tant que paramètre à chacune de ces méthodes. Chaque méthode insère l’entité s’il n’existe pas. Si l’entité existe déjà, **insertOrMergeEntity** met à jour les valeurs de propriété si les propriétés existent déjà et ajoute de nouvelles propriétés s’ils n’existent pas, tandis que **insertOrReplaceEntity** remplace complètement une entité existante. L’exemple suivant montre comment utiliser **insertOrMergeEntity**. Si l’entité avec `PartitionKey` « tasksSeattle » et `RowKey` « 1 » n’existe pas déjà, il est insérée. Toutefois, si elle a été inséré précédemment (comme illustré dans l’exemple ci-dessus), la `DueDate` propriété est mise à jour et la `Status` propriété sera ajoutée. La `Description` et `Location` sont également mises à jour, mais avec des valeurs qui efficacement les laisser inchangées. Si ces deux dernières propriétés ont été pas ajoutées comme indiqué dans l’exemple, mais figuraient sous l’entité cible, leurs valeurs existantes reste inchangées.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>Récupérer une seule entité

La méthode **TableRestProxy -> getEntity** vous permet de récupérer une seule entité en recherchant son `PartitionKey` et `RowKey`. Dans l’exemple ci-dessous, la clé de partition `tasksSeattle` et la clé de ligne `1` passé à la méthode **getEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>Récupérer toutes les entités dans une partition

Requêtes entité sont construits à l’aide de filtres (pour plus d’informations, voir [requêtes des Tables et entités][filters]). Pour récupérer toutes les entités de partition, utilisez le filtre « PartitionKey eq *nom_partition*». L’exemple suivant montre comment récupérer toutes les entités dans le `tasksSeattle` partition en passant d’un filtre à la méthode **queryEntities** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Récupérer un sous-ensemble d’entités dans une partition

Le même modèle utilisé dans l’exemple précédent peut servir à récupérer un sous-ensemble d’entités dans une partition. Le sous-ensemble d’entités vous récupérez sont déterminées par le filtre que vous utilisez (pour plus d’informations, voir [requêtes des Tables et entités][filters]). L’exemple suivant montre comment utiliser un filtre pour récupérer toutes les entités comportant un spécifique `Location` et un `DueDate` inférieure à une date spécifiée.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>Récupérer un sous-ensemble de propriétés de l’entité

Une requête peut récupérer un sous-ensemble de propriétés de l’entité. Cette technique, appelée *projection*, réduit la bande passante et peut améliorer les performances des requêtes, en particulier pour les entités volumineuses. Pour spécifier une propriété à récupérer, passez le nom de la propriété à la méthode de **requête -> addSelectField** . Vous pouvez appeler cette méthode plusieurs fois pour ajouter d’autres propriétés. Après avoir exécuté **TableRestProxy -> queryEntities**, les entités renvoyées disposeront uniquement les propriétés sélectionnées. (Si vous voulez retourner un sous-ensemble d’entités de Table, utilisez un filtre comme indiqué dans les requêtes ci-dessus.)

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>Mettre à jour une entité

Une entité existante pouvant être mis à jour en utilisant les méthodes **entité -> DéfinirPropriété** et **entité -> addProperty** sur l’entité, puis en appelant **TableRestProxy -> updateEntity**. L’exemple suivant extrait une entité, modifie une propriété, supprime une autre propriété et ajoute une nouvelle propriété. Notez que vous pouvez supprimer une propriété en définissant sa valeur **null**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>Supprimer une entité

Pour supprimer une entité, passez le nom de table et de l’entité `PartitionKey` et `RowKey` à la méthode **TableRestProxy -> deleteEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Notez que pour les contrôles d’accès concurrentiels, vous pouvez définir l’Etag pour une entité à supprimer en utilisant la méthode **DeleteEntityOptions -> setEtag** et en passant l’objet **DeleteEntityOptions** **deleteEntity** comme une quatrième paramètre.

## <a name="batch-table-operations"></a>Opérations de table par lots

La méthode **TableRestProxy -> lot** permet d’exécuter plusieurs opérations en une seule demande. Le modèle ici implique l’ajout d’opérations à **BatchRequest** objet, puis en passant l’objet **BatchRequest** à la méthode **TableRestProxy -> lot** . Pour ajouter une opération à un objet **BatchRequest** , vous pouvez appeler une des méthodes suivantes plusieurs fois :

* **addInsertEntity** (ajoute une opération insertEntity)
* **addUpdateEntity** (ajoute une opération updateEntity)
* **addMergeEntity** (permet de créer une opération de mergeEntity)
* **addInsertOrReplaceEntity** (ajoute une opération insertOrReplaceEntity)
* **addInsertOrMergeEntity** (ajoute une opération insertOrMergeEntity)
* **addDeleteEntity** (permet de créer une opération de deleteEntity)

L’exemple suivant montre comment effectuer des opérations **insertEntity** et **deleteEntity** dans une seule demande :

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Pour plus d’informations sur le traitement par lots opérations de Table, consultez [Exécution des Transactions groupe entité][entity-group-transactions].

## <a name="delete-a-table"></a>Supprimer un tableau

Enfin, pour supprimer un tableau, passez le nom de table à la méthode **TableRestProxy -> deleteTable** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base du service Table Azure, suivez ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- Visitez le [blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Pour plus d’informations, voir également le [Centre de développement PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
