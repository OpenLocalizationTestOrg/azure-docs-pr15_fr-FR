<properties
    pageTitle="Comment utiliser le stockage de Table à partir de Python | Microsoft Azure"
    description="Stocker des données structurées dans le cloud à l’aide de stockage de Table Azure, un magasin de données NoSQL."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>Comment utiliser le stockage de Table à partir de Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous montre comment effectuer des scénarios courants en utilisant le service de stockage de Table Azure. Les exemples sont écrits dans Python et utilisent le [Kit de développement de Microsoft Azure stockage pour Python]. Les scénarios couverts comprennent la création et suppression d’une table, en plus d’insertion et interrogation des entités dans un tableau.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Créer une table

L’objet **TableService** vous permet de travailler avec les services de tableau. Le code suivant crée un objet **TableService** . Ajoutez le code dans la partie supérieure de n’importe quel fichier Python dans laquelle vous souhaitez accéder par programme stockage Azure :

    from azure.storage.table import TableService, Entity

Le code suivant crée un objet **TableService** à l’aide de la clé de compte et nom de compte de stockage.  Remplacez « mon compte » et « mykey » par votre nom de compte et la clé.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité, tout d’abord créer un dictionnaire ou une entité qui définit les noms de propriété entité et les valeurs. Notez que pour chaque entité, vous devez spécifier **PartitionKey** et **RowKey**. Voici les identificateurs uniques de vos entités. Vous pouvez interroger à l’aide de ces valeurs bien plus vite que vous pouvez interroger vos autres propriétés. Le système utilise **PartitionKey** pour distribuer automatiquement les entités de table sur le nombre de nœuds de stockage.
Des personnes qui ont la même **PartitionKey** sont stockées sur le même nœud. **RowKey** est l’ID unique de l’entité au sein de la partition auquel il appartient.

Pour ajouter une entité à votre tableau, passer un objet de dictionnaire à la **Insérer\_entité** méthode.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

Vous pouvez également passer une instance de la classe **entité** pour la **Insérer\_entité** méthode.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>Mettre à jour une entité

Ce code montre comment remplacer l’ancienne version d’une entité existante par une version mise à jour.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

Si l’entité est mis à jour n’existe pas, l’opération de mise à jour échouera. Si vous voulez stocker une entité qu’il existe ou non avant, utilisez **Insérer\_ou\_replace_entity**.
Dans l’exemple suivant, le premier appel remplacera l’entité existante. Le deuxième appel insère une nouvelle entité, depuis aucune entité avec la spécifiée **PartitionKey** et **RowKey** existe dans la table.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>Modifier un groupe d’entités

Parfois, il est préférable d’envoyer plusieurs opérations collaborer dans un lot pour garantir atomique traitement par le serveur. Pour ce faire, vous utilisez la classe **TableBatch** . Si vous ne souhaitez pas soumettre le lot, vous appelez **Valider\_lot**. Notez que toutes les entités doivent être placé dans la même partition afin d’être modifié comme un lot. L’exemple ci-dessous additionne deux entités dans un lot.

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

Lots peuvent également être utilisés avec la syntaxe du Gestionnaire de contexte :

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>Requête pour une entité

Pour interroger une entité dans un tableau, utilisez la **obtenir\_entité** méthode en passant **PartitionKey** et **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>Interroger un ensemble d’entités

Cet exemple recherche que toutes les tâches de Seattle basé sur **PartitionKey**.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête

Une requête à une table pouvez récupérer quelques propriétés d’une entité.
Cette technique, appelée *projection*, réduit la bande passante et peut améliorer les performances des requêtes, en particulier pour les entités volumineuses. Utilisez le paramètre **Sélectionnez** et transmettre les noms des propriétés que vous voulez importer au client.

La requête dans le code suivant renvoie uniquement les descriptions des entités de la table.

[AZURE.NOTE] L’extrait suivant fonctionne uniquement avec le service de stockage cloud. Ce n’est pas pris en charge par l’émulateur de stockage.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>Supprimer une entité

Vous pouvez supprimer une entité à l’aide de sa clé partition et ligne.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>Supprimer un tableau

Le code suivant supprime un tableau à partir d’un compte de stockage.

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de stockage de Table, suivez ces liens pour en savoir plus.

- [Centre de développement Python](/develop/python/)
- [Services de stockage Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l’équipe stockage Azure]
- [Stockage de Microsoft Azure SDK pour Python]

[Blog de l’équipe de stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Stockage de Microsoft Azure SDK pour Python]: https://github.com/Azure/azure-storage-python
