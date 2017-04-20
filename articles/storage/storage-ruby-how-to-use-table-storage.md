<properties
    pageTitle="Comment utiliser le stockage de Table Azure de Ruby | Microsoft Azure"
    description="Stocker des données structurées dans le cloud à l’aide de stockage de Table Azure, un magasin de données NoSQL."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>Comment utiliser le stockage de Table Azure de Ruby

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous montre comment effectuer des scénarios courants au moyen du service de Table Azure. Ces exemples sont écrits à l’aide de l’API Ruby. Les scénarios présentés incluent la **Création et suppression d’une table, insertion et interrogation des entités dans un tableau**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Créer une application prononciation

Pour obtenir des instructions comment créer une application prononciation, consultez [Ruby sur application Web Rails sur un ordinateur virtuel Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).


## <a name="configure-your-application-to-access-storage"></a>Configurez votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous devez télécharger et utiliser la prononciation package azure qui comprend un ensemble de bibliothèques de commodité communiquer avec les services de stockage reste.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems permet d’obtenir le package

1. Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Dans la fenêtre de commande pour installer la marque et les dépendances, tapez **marque installer azure** .

### <a name="import-the-package"></a>Importer le package

Utilisez votre éditeur de texte, ajoutez le code suivant en haut du fichier prononciation dans laquelle vous souhaitez utiliser le stockage :

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion de stockage Azure

Le module azure lira les variables d’environnement **AZURE\_stockage\_compte** et **AZURE\_stockage\_ACCESS\_clé** pour plus d’informations requises pour vous connecter à votre compte de stockage Azure. Si ces variables d’environnement ne sont pas définis, vous devez spécifier les informations de compte avant d’utiliser **Azure::TableService** avec le code suivant :

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Pour obtenir ces valeurs à partir d’un classique ou le compte de stockage Gestionnaire de ressources dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Naviguez vers le compte de stockage que vous souhaitez utiliser.
3. Dans la carte de paramètres sur la droite, cliquez sur **Les touches d’accès**.
4. Dans la carte de touches d’accès qui s’affiche, vous verrez la touche d’accès rapide 1 et 2 touche d’accès rapide. Vous pouvez utiliser des options suivantes. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers. 

Pour obtenir ces valeurs à partir d’un compte de stockage classique dans le portail Azure classique :

1. Connectez-vous au [portail Azure classique](https://manage.windowsazure.com).
2. Naviguez vers le compte de stockage que vous souhaitez utiliser.
3. Cliquez sur **Gérer les touches d’accès** dans la partie inférieure du volet de navigation.
4. Dans la boîte de dialogue qui apparaît, vous verrez le nom de compte de stockage, la clé primaire access et la touche d’accès secondaire. Pour touche d’accès rapide, vous pouvez utiliser l’option principal ou secondaire celui. 
5. Cliquez sur l’icône Copier pour copier la clé dans le Presse-papiers.

## <a name="create-a-table"></a>Créer une table

L’objet **Azure::TableService** vous permet de travailler avec les tables et entités. Pour créer un tableau, utilisez la **créer\_table()** méthode. L’exemple suivant crée une table ou imprimer l’erreur le cas échéant.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>Ajouter une entité à une table

Pour ajouter une entité, tout d’abord créer un objet de hachage qui définit les propriétés de votre entité. Notez que pour chaque entité, vous devez spécifier un **PartitionKey** et **RowKey**. Ils sont les identificateurs uniques de vos entités et sont des valeurs qui peuvent être interrogées beaucoup plus rapidement que d’autres propriétés de votre. Stockage Azure utilise **PartitionKey** pour distribuer automatiquement entités du tableau sur grand nombre de nœuds de stockage. Entités avec la même **PartitionKey** sont stockées sur le même nœud. Le **RowKey** est l’ID unique de l’entité au sein de la partition qu'auquel il appartient.

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>Mettre à jour une entité

Il existe plusieurs méthodes disponibles pour mettre à jour une entité existante :

* **mettre à jour\_entity() :** Mettre à jour une entité existante en le remplaçant.
* **fusion\_entity() :** Met à jour une entité existante en fusionnant les nouvelles valeurs de propriété dans l’entité existante.
* **Insérer\_ou\_fusion\_entity() :** Met à jour une entité existante en le remplaçant. Il n’existe aucune entité, permet d’insérer un nouvel identifiant :
* **Insérer\_ou\_remplacer\_entity() :** Met à jour une entité existante en fusionnant les nouvelles valeurs de propriété dans l’entité existante. Si aucune entité n’existe, une nouvelle visualisation est insérée.

L’exemple suivant illustre la mise à jour une entité à l’aide **mettre à jour\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Avec **mettre à jour\_entity()** et **fusion\_entity()**, si l’entité que vous mettez à jour n’existe pas l’opération de mise à jour échouera. Par conséquent, si vous souhaitez stocker une entité indépendamment si elle existe déjà, vous devez utiliser **Insérer\_ou\_remplacer\_entity()** ou **Insérer\_ou\_fusion\_entity()**.

## <a name="work-with-groups-of-entities"></a>Utilisation des groupes d’entités

Parfois, il est préférable d’envoyer plusieurs opérations collaborer dans un lot pour garantir atomique traitement par le serveur. Pour ce faire, vous commencez par créer un objet **lot** , puis utilisez le **exécuter\_batch()** méthode sur **TableService**. L’exemple suivant illustre l’envoi de deux entités RowKey 2 et 3 dans un lot. Notez qu’elle fonctionne uniquement pour les entités avec la même PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>Requête pour une entité

Pour interroger une entité dans un tableau, utilisez la **obtenir\_entity()** méthode, en passant le nom de table, **PartitionKey** et **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>Interroger un ensemble d’entités

Pour interroger un ensemble d’entités dans un tableau, créez un objet de hachage requête et utilisez la **requête\_entities()** méthode. L’exemple suivant illustre l’obtention de toutes les entités avec la même **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Si le jeu de résultats est trop volumineux pour une seule requête à retourner, un jeton de continuation est retourné que vous pouvez utiliser pour récupérer les pages suivantes.

## <a name="query-a-subset-of-entity-properties"></a>Un sous-ensemble des propriétés de l’entité de requête

Une requête à une table pouvez récupérer quelques propriétés d’une entité. Cette technique, appelée « projection », réduit la bande passante et peut améliorer les performances des requêtes, en particulier pour les entités volumineuses. Utilisez la clause select et passez les noms des propriétés que vous voulez obtenir au client.

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>Supprimer une entité

Pour supprimer une entité, utilisez la **Supprimer\_entity()** méthode. Vous devez passer le nom de la table qui contient l’entité, la PartitionKey et RowKey de l’entité.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>Supprimer un tableau

Pour supprimer un tableau, utilisez la **Supprimer\_table()** méthode et passez le nom de la table que vous voulez supprimer.

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les tâches de stockage plus complexes, suivez ces liens :

- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Référentiel de [Azure SDK pour Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) GitHub
