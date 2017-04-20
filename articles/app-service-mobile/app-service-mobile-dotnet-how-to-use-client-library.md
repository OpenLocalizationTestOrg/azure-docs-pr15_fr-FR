<properties
    pageTitle="Utilisation de la bibliothèque de client géré application Service Mobile applications (Windows | Xamarin) | Microsoft Azure"
    description="Découvrez comment utiliser un client .NET pour Azure Application Service Mobile applications avec les applications Windows et Xamarin."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Comment utiliser le client géré pour applications Mobile Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>Vue d’ensemble

Ce guide vous montre comment effectuer des scénarios courants à l’aide de la bibliothèque client géré pour Azure Application Service Mobile applications pour Windows et applications Xamarin. Si vous débutez dans les applications Mobile, vous devez prendre en compte tout d’abord fin le [démarrage rapide pour les applications Mobile Azure] [ 1] didacticiel. Dans ce guide, nous concentrer sur le Kit de développement côté client géré. Pour en savoir plus sur les kits de développement côté serveur pour les applications mobiles, voir la documentation pour le [Kit de développement .NET Server] [ 2] ou le [Kit de développement Node.js Server][3].

## <a name="reference-documentation"></a>Documentation de référence

La documentation de référence pour le Kit de développement logiciel client se trouve ici : [référence du client Azure Mobile applications .NET][4].
Plusieurs exemples de client sont également disponibles dans le [référentiel Azure exemples GitHub][5].

## <a name="supported-platforms"></a>Plateformes prises en charge

La plateforme .NET prend en charge les plates-formes suivantes :

* Mises à jour de Xamarin Android pour API 19 à 24 (KitKat via la commande)
* Mises à jour de Xamarin iOS pour iOS 8.0 et versions ultérieures
* Plateforme Windows universel
* Windows Phone 8.1
* Windows Phone 8.0 à l’exception des applications Silverlight

L’authentification « flux de serveur » utilise un mode d’affichage Web pour l’interface utilisateur présentée.  Si l’appareil n’est pas en mesure de présenter un WebView UI, d’autres méthodes d’authentification sont nécessaires.  Ce SDK n’est donc pas approprié pour le type d’espion ou de la même manière des périphériques restreints.

##<a name="setup"></a>Le programme d’installation et configuration requise

Nous part du principe que vous avez déjà créé et publié votre projet principal application Mobile, qui inclut au moins une table.  Dans le code utilisé dans cette rubrique, la table est nommée `TodoItem` et qu’il possède les colonnes suivantes : `Id`, `Text`, et `Complete`. Ce tableau est la même table créée lorsque vous effectuez le démarrage [applications Mobile Azure rapide].

Le type de client-side tapé correspondant en c# est la classe suivante :

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

La [JsonPropertyAttribute] [ 6] est utilisé pour définir le mappage *PropertyName* entre le type de client et de la table.

Pour découvrir comment créer des tables dans votre système principal applications Mobile, voir les informations contenues dans la [rubrique .NET Server SDK] [ 7] ou à la [rubrique Node.js Server SDK][8]. Si vous avez créé la principale application Mobile dans le portail Azure à l’aide de démarrage rapide, vous pouvez également utiliser le paramètre de **tables faciles** dans le [portail Azure].

###<a name="how-to-install-the-managed-client-sdk-package"></a>Comment : installer le module de kit de développement logiciel client géré

Utilisez une des méthodes suivantes pour installer le package de kit de développement logiciel client géré pour les applications mobiles de [NuGet][9]:

+ **Visual Studio** Avec le bouton droit de votre projet, cliquez sur **Gérer les Packages NuGet**, recherchez la `Microsoft.Azure.Mobile.Client` du package, puis cliquez sur **installer**.

+ **Xamarin Studio** Avec le bouton droit de votre projet, cliquez sur **Ajouter** > **Ajouter des Packages NuGet**, recherchez la `Microsoft.Azure.Mobile.Client `du package, puis cliquez sur **Ajouter un Package**.

Dans votre fichier d’activité principal, pensez à ajouter l’instruction **using** suivante :

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>Comment : utiliser des symboles de débogage dans Visual Studio

Les symboles de l’espace de noms Microsoft.Azure.Mobile sont disponibles sur [SymbolSource][10].  Reportez-vous aux [instructions SymbolSource] [ 11] intégrer SymbolSource avec Visual Studio.

##<a name="create-client"></a>Créer le client applications Mobile

Le code suivant crée la [MobileServiceClient] [ 12] objet qui est utilisé pour accéder à votre serveur principal application Mobile.

    var client = new MobileServiceClient("MOBILE_APP_URL");

Dans le code précédent, remplacez `MOBILE_APP_URL` avec l’URL de l’application Mobile principal, qui se trouve dans la carte pour la principale application Mobile dans le [portail Azure]. L’objet MobileServiceClient doit être un singleton.

## <a name="work-with-tables"></a>Utiliser des tableaux

La section suivante explique en détail comment rechercher et extraire des enregistrements et modifier les données du tableau.  Les rubriques suivantes :

* [Créer une référence de table](#instantiating)
* [Interroger des données](#querying)
* [Filtrer les données renvoyées](#filtering)
* [Tri renvoyé des données](#sorting)
* [Renvoyer les données dans des pages](#paging)
* [Sélectionner des colonnes spécifiques](#selecting)
* [Rechercher un enregistrement par Id](#lookingup)
* [Gérer des requêtes non typées en](#untypedqueries)
* [Insertion de données](#inserting)
* [Mise à jour des données](#updating)
* [Supprimer des données](#deleting)
* [Résolution des conflits et simultané](#optimisticconcurrency)
* [Liaison à une Interface utilisateur Windows](#binding)
* [Modification de la taille de Page](#pagesize)

###<a name="instantiating"></a>Comment : créer une référence de table

Tout le code qui accède à ou modifie les données dans un tableau et fonctions des appels dans le `MobileServiceTable` objet. Obtenir une référence à la table en appelant la méthode [GetTable] , comme suit :

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

L’objet retourné utilise le modèle de sérialisation tapé. Un modèle de sérialisation non typée est également prise en charge. D’exemple suivant [crée une référence à une table sans type]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Dans les requêtes non typées, vous devez spécifier la chaîne de requête OData sous-jacente.

###<a name="querying"></a>Comment : interroger des données à partir de votre application Mobile

Cette section décrit comment émettre des requêtes sur le serveur principal application Mobile, qui inclut les fonctionnalités suivantes :

- [Filtrer les données renvoyées](#filtering)
- [Tri renvoyé des données](#sorting)
- [Renvoyer les données dans des pages](#paging)
- [Sélectionner des colonnes spécifiques](#selecting)
- [Rechercher des données par code](#lookingup)

>[AZURE.NOTE]Une taille de page serveur par l’effort est appliquée pour empêcher que toutes les lignes renvoyées.  Pagination empêche que négative ayant un impact sur le service de requêtes par défaut pour les grands ensembles de données.  Pour renvoyer plus de 50 lignes, utilisez la `Skip` et `Take` méthode, comme décrit dans [renvoyer les données dans les pages].

###<a name="filtering"></a>Comment : filtrer les données renvoyées

Le code suivant illustre comment filtrer des données en incluant une `Where` clause dans une requête. Elle renvoie tous les éléments de `todoTable` dont `Complete` propriété est égale à `false`. La fonction [où] s’applique une ligne filtrage prédicat à la requête par rapport à la table.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Vous pouvez consulter l’URI de la demande envoyée vers le serveur principal en utilisant le logiciel inspection message, tels que les outils de développement de navigateur ou [Fiddler]. Si vous examinez l’URI de requête, notez que la chaîne de requête a été modifiée :

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Cette requête OData est convertie en une requête SQL par le Kit de développement de serveur :

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

La fonction que celle qu’elle reçoit le `Where` méthode peut avoir un nombre arbitraire de conditions.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

Cet exemple serait traduit dans une requête SQL par le Kit de développement de serveur :

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Cette requête peut également être divisée en plusieurs clauses :

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Les deux méthodes sont équivalents et peuvent être utilisés de manière interchangeable.  La première option&mdash;concaténer deux prédicats dans une requête&mdash;est plus compact et recommandé.

La `Where` clause prend en charge les opérations d’être traduite dans le sous-ensemble OData. Sont les opérations suivantes :

* Opérateurs relationnels (==, ! = <, < =, >, > =),
* Opérateurs arithmétiques (+, -, /, *, %),
* Numéro de précision (Math.Floor, Math.Ceiling)
* Fonctions de chaîne (longueur, sous-chaîne, remplacer, IndexOf, StartsWith, EndsWith)
* Propriétés de date (année, mois, jour, heure, Minute, seconde)
* Accéder aux propriétés d’un objet, et
* Expressions combinant aucune de ces opérations.

Quand vous déterminez que le Kit de développement serveur prend en charge, vous pouvez envisager de la [documentation présentée sous d’OData v3].

###<a name="sorting"></a>Comment : trier les données renvoyées

Le code suivant montre comment trier les données en utilisant une fonction [OrderBy] ou [OrderByDescending] dans la requête. Renvoie les éléments de `todoTable` trié par ordre croissant par la `Text` champ.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Comment : retourner des données dans des pages

Par défaut, le serveur principal renvoie uniquement les 50 premières lignes. Vous pouvez augmenter le nombre de lignes renvoyées par la méthode [prendre] . Utiliser `Take` ainsi que la méthode [Ignorer] pour demander une « page » du dataset total renvoyée par la requête spécifique. La requête suivante, lorsqu’elle est exécutée, renvoie les valeurs les plus élevées trois dans le tableau.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

La requête suivante révisée ignore les trois premiers résultats et renvoie le résultat suivant trois. Cette requête génère la deuxième « page » de données, où la taille de page est trois éléments.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

La méthode [IncludeTotalCount] requiert le nombre total pour _tous les_ enregistrements qui aurait été retournés, en ignorant toute clause/limite de pagination spécifiée :

    query = query.IncludeTotalCount();

Dans une application réels, vous pouvez utiliser des requêtes semblables à l’exemple précédent avec un contrôle de pagination ou d’une interface utilisateur comparable pour naviguer entre les pages.

>[AZURE.NOTE]Pour remplacer la limite de 50 ligne dans une application Mobile principale, vous devez également appliquer [EnableQueryAttribute] à la méthode GET publique et spécifier le comportement de pagination. Lorsqu’elle est appliquée à la méthode, la valeur maximale renvoyée lignes à 1000 définit les éléments suivants :
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Comment : sélectionner des colonnes spécifiques

Vous pouvez spécifier l’ensemble de propriétés à inclure dans les résultats en ajoutant une clause [Select] à votre requête. Par exemple, le code suivant montre comment sélectionner qu’un seul champ et également comment sélectionner et mettre en forme de plusieurs champs :

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Toutes les fonctions décrites jusque sont additif, afin que nous puissions conserver liant. Chaque appel chaîne affecte plus de la requête. Un autre exemple :

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Comment : rechercher des données par code

La fonction [LookupAsync] peut être utilisée pour rechercher des objets à partir de la base de données avec un ID particulier.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Comment : exécuter des requêtes non typées

Lorsque vous exécutez une requête à l’aide d’un objet de la table sans type, vous devez spécifier explicitement la chaîne de requête OData en appelant [ReadAsync], comme dans l’exemple suivant :

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous revenir valeurs JSON que vous pouvez utiliser comme un ensemble de propriétés. Pour plus d’informations sur JToken et Newtonsoft Json.NET, consultez le site [Json.NET] .

### <a name="inserting"></a>Comment : insérer des données dans une application Mobile principale

Tous les types de clients doivent contenir un membre nommé **Id**, qui est par défaut une chaîne. Cet **Id** est requis pour effectuer des opérations CRUD et pour la synchronisation hors connexion. Le code suivant illustre comment utiliser la méthode [InsertAsync] pour insérer de nouvelles lignes dans une table. Le paramètre contient les données doit être insérée en tant qu’objet .NET.

    await todoTable.InsertAsync(todoItem);

Si une valeur d’ID unique personnalisée n’est pas incluse dans le `todoItem` pendant une insertion, un GUID est généré par le serveur.
Vous pouvez récupérer l’Id généré en inspectant l’objet une fois que l’appel est renvoyé.

Pour insérer des données non typées, vous pouvez tirer parti de Json.NET :

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Voici un exemple d’utilisation d’une adresse de messagerie en tant qu’un id de chaîne unique :

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Utilisation des valeurs d’ID

Applications Mobile prend en charge les valeurs de chaîne personnalisées uniques pour la colonne **id** de la table. Une valeur de chaîne permet aux applications d’utiliser des valeurs personnalisées, telles que les adresses de messagerie ou les noms d’utilisateur pour le code.  ID de chaîne vous fournit les avantages suivants :

* ID sont générés sans modifier accidentellement un aller et retour à la base de données.
* Enregistrements sont plus faciles à utiliser lors de différentes tables ou des bases de données de fusion.
* Valeurs ID peuvent s’intègrent mieux logique d’une application.

Lorsqu’une valeur d’ID de chaîne n’est pas définie sur un enregistrement inséré, le système principal de l’application Mobile génère une valeur unique pour le code. Vous pouvez utiliser la méthode [Guid.NewGuid] pour générer vos propres valeurs d’ID, sur le client ou dans le système principal.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Comment : modifier des données dans une application Mobile principale

Le code suivant illustre comment utiliser la méthode [UpdateAsync] pour mettre à jour un enregistrement existant avec le même ID avec les nouvelles informations. Le paramètre contient les données à mettre à jour dans un objet .NET.

    await todoTable.UpdateAsync(todoItem);

Pour mettre à jour des données non typées, vous pouvez tirer parti de [Json.NET] comme suit :

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Un `id` champ doit être spécifié lorsque vous effectuez une mise à jour. Le serveur principal utilise le `id` champ afin d’identifier les lignes à mettre à jour. La `id` champ peut être obtenu à partir du résultat de la `InsertAsync` appeler. Un `ArgumentException` est levée si vous essayez de mettre à jour un élément sans fournir le `id` valeur.

###<a name="deleting"></a>Comment : supprimer des données dans une application Mobile principale

Le code suivant illustre comment utiliser la méthode [DeleteAsync] pour supprimer une instance existante. L’instance est identifiée par la `id` champ ensemble sur la `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Pour supprimer des données non typées, vous pouvez tirer parti de Json.NET comme suit :

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Lorsque vous faites une demande de suppression, un ID est obligatoire. D’autres propriétés ne sont pas transmises au service ou sont ignorées au niveau du service. Le résultat d’une `DeleteAsync` appel est généralement `null`. L’ID de passer peut être obtenu à partir du résultat de la `InsertAsync` appeler. A `MobileServiceInvalidOperationException` est levée lorsque vous tentez de supprimer un élément sans spécifier la `id` champ.

###<a name="optimisticconcurrency"></a>Comment : utiliser simultané de résolution des conflits

Deux ou plusieurs clients peuvent écrire des modifications au même élément en même temps. Sans détection de conflit, la dernière écriture remplace les mises à jour précédentes. **Contrôle de concurrence optimiste** suppose que chaque transaction peut s’engager et donc n’utilise pas de verrouillage de la ressource.  Avant de valider une transaction, contrôle de concurrence optimiste vérifie qu’aucune autre transaction n’a modifié les données. Si les données ont été modifiées, la validation transaction est annulée.

Applications Mobile prend en charge le contrôle de concurrence optimiste grâce au suivi des modifications pour chaque élément à l’aide du `version` colonne de propriété système qui est défini pour chaque table dans votre système principal de l’application Mobile. Chaque mise à jour d’un enregistrement, applications Mobile définit la `version` propriété pour l’enregistrement d’une nouvelle valeur. Lors de chaque requête mise à jour, le `version` propriété de l’enregistrement inclus dans la demande est comparée à la même propriété pour l’enregistrement sur le serveur. Si la version passé avec la demande ne correspond pas à la version serveur, puis la bibliothèque cliente génère une `MobileServicePreconditionFailedException<T>` exception. Le type inclus avec l’exception est l’enregistrement à partir du serveur principal contenant la version de serveurs de l’enregistrement. L’application peut ensuite utiliser ces informations pour décider s’il faut exécuter la requête mise à jour à l’aide de la bonne `version` valeur à partir du serveur principal pour valider les modifications.

Définir une colonne dans la classe de table pour le `version` propriété système pour activer simultané. Par exemple :

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Applications à l’aide de tableaux non typée activer simultané en définissant la `Version` indicateur sur le `SystemProperties` de la table comme suit.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Outre l’activation simultané, vous devez également intercepter la `MobileServicePreconditionFailedException<T>` exception dans votre code lors de l’appel [UpdateAsync].  Résoudre le conflit en appliquant la bonne `version` pour l’enregistrement mis à jour et l’appel [UpdateAsync] avec l’enregistrement résolu. Le code suivant montre comment résoudre un conflit d’écriture détectée une fois :

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

Pour plus d’informations, consultez la rubrique [Synchronisation des données en mode hors connexion dans les applications Mobile Azure] .

###<a name="binding"></a>Comment : données applications Mobile liaison à une interface utilisateur Windows

Cette section indique comment afficher des objets de données retournées à l’aide des éléments d’interface utilisateur dans une application Windows.  L’exemple suivant crée une liaison à la source de la liste avec une requête pour les éléments incomplètes. La [MobileServiceCollection] crée une collection de liaison Mobile applications prenant en charge.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Certains contrôles dans le runtime managé prend en charge une interface appelée [ISupportIncrementalLoading]. Cette interface permet aux contrôles demander des données supplémentaires lorsque l’utilisateur fait défiler. Il est prise en charge intégrée pour cette interface pour les applications Windows universels via [MobileServiceIncrementalLoadingCollection]qui gère automatiquement les appels à partir des contrôles. Utiliser `MobileServiceIncrementalLoadingCollection` dans les applications Windows comme suit :

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Pour utiliser la nouvelle collection dans des applications Windows Phone 8 et « Silverlight », utilisez la `ToCollection` méthodes d’extension sur `IMobileServiceTableQuery<T>` et `IMobileServiceTable<T>`. Pour charger les données, appelez `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Lorsque vous utilisez la collection créée par un appel `ToCollectionAsync` ou `ToCollection`, vous obtenez une collection de sites qui peut être liés à des contrôles de l’interface utilisateur.  Cette collection est prenant en charge la pagination.  Étant donné que la collection de chargement de données à partir du réseau, parfois le chargement échoue. Pour gérer de tels échecs, remplacer la `OnException` méthode sur `MobileServiceIncrementalLoadingCollection` traiter les exceptions résultant des appels vers `LoadMoreItemsAsync`.

Prendre en compte si votre table comporte plusieurs champs mais que vous souhaitez uniquement afficher certains d'entre eux dans votre contrôle. Vous pouvez utiliser le guide dans la section précédente «[Sélectionner des colonnes spécifiques](#selecting)» pour sélectionner des colonnes spécifiques à afficher dans l’interface utilisateur.

###<a name="pagesize"></a>Modifier la taille de Page

Applications mobiles Azure renvoie un maximum de 50 éléments par demande par défaut.  Vous pouvez modifier la taille de pagination en augmentant la taille maximale de page sur le client et le serveur.  Pour augmenter la taille de page demandée, spécifiez `PullOptions` lors de l’utilisation `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

En supposant que vous avez effectué la `PageSize` égale ou supérieure à 100 au sein du serveur, une requête renvoie les éléments jusqu'à 100.

##<a name="#offlinesync"></a>Utiliser des tableaux en mode hors connexion

Tables en mode hors connexion utilisent une magasin à des données locales SQLite pour une utilisation en mode hors connexion.  Table toutes les opérations sont effectuées par rapport à l’ordinateur local SQLite stocker au lieu de la base de serveur distant.  Pour créer une table en mode hors connexion, tout d’abord préparer votre projet :

1. Dans Visual Studio, cliquez sur la solution > **Gérer les Packages NuGet pour Solution...**, puis recherchez et installez le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** pour tous les projets dans la solution.

2. (Facultatif) Pour prendre en charge les appareils Windows, installez un des packages runtime SQLite suivants :

    * **Runtime Windows 8.1 :** Installer [SQLite pour Windows 8.1][3].
    * **Windows Phone 8.1 :** Installer [SQLite pour Windows Phone 8.1][4].
    * **Plateforme Windows universel** Installer [SQLite l’universel universel de Windows][5].

3. (Facultatif). Pour les appareils Windows, cliquez sur **références** > **Ajouter une référence**, développez le dossier **Windows** > **Extensions**, puis activer les appropriées **SQLite pour Windows** SDK ainsi que le Kit de développement **Windows Visual C++ 2013 Runtime pour** .
    Les noms de SQLite SDK varient légèrement avec chaque plateforme Windows.

Avant de pouvoir créer une référence de table, le magasin local doit être préparé :

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

L’initialisation du magasin est normalement effectuée immédiatement après que le client est créé.  **OfflineDbPath** doit être un nom de fichier pouvant être utilisée sur toutes les plates-formes prises en charge.  Si le chemin d’accès est un chemin d’accès complet (autrement dit, elle commence par une barre oblique), ce chemin d’accès est utilisée.  Si le chemin d’accès n’est pas pleinement qualifiée, le fichier est placé dans un emplacement spécifique de la plate-forme.

* Pour les appareils iOS et Android, le chemin d’accès par défaut est le dossier « Fichiers personnels ».
* Pour les appareils Windows, le chemin d’accès par défaut est le dossier « AppData » spécifiques à l’application.

Une référence de tableau peut être obtenue à l’aide de la `GetSyncTable<>` méthode :

    var table = client.GetSyncTable<TodoItem>();

Vous n’avez pas besoin de s’authentifier pour utiliser une table en mode hors connexion.  Il vous suffit de vous authentifier lorsque vous communiquez avec le service principal.

###<a name="syncoffline"></a>La synchronisation d’une Table en mode hors connexion

Tables en mode hors connexion ne sont pas synchronisés avec le serveur principal par défaut.  La synchronisation est fractionnée en deux parties.  Vous pouvez distribuer les modifications séparément de télécharger les nouveaux éléments.  Voici une méthode de synchronisation type :

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
                "allTodoItems",
                this.todoTable.CreateQuery());
        }
        catch (MobileServicePushFailedException exc)
        {
            if (exc.PushResult != null)
            {
                syncErrors = exc.PushResult.Errors;
            }
        }

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
        if (syncErrors != null)
        {
            foreach (var error in syncErrors)
            {
                if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                {
                    //Update failed, reverting to server's copy.
                    await error.CancelAndUpdateItemAsync(error.Result);
                }
                else
                {
                    // Discard local change.
                    await error.CancelAndDiscardItemAsync();
                }

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Si le premier argument `PullAsync` est null, puis synchroniser incrémentiel n’est pas utilisé.  Chaque opération de synchronisation récupère tous les enregistrements.

Le Kit de développement effectue une implicite `PushAsync()` avant l’extraction des enregistrements.

Gestion de conflit se produit sur un `PullAsync()` méthode.  Vous pouvez traiter les conflits de la même façon que les tables en ligne.  Le conflit se produit lorsque `PullAsync()` est appelée au lieu de pendant l’insertion, la mise à jour ou le supprimer. Si plusieurs conflits se produisent, ils sont regroupés dans une seule MobileServicePushFailedException.  Gérer chaque défaillance séparément.

##<a name="#customapi"></a>Travailler avec une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison personnalisés qui exposent les fonctionnalités du serveur qui ne pas établir une correspondance entre une instruction insert, mettre à jour, supprimer ou opération de lecture. En utilisant une API personnalisée, vous pouvez avoir plus de contrôle sur la messagerie, notamment la lecture et définir des en-têtes de message HTTP et définir un format de corps du message que JSON.

Vous appelez une API personnalisée en appelant une des méthodes [InvokeApiAsync] sur le client. Par exemple, la ligne suivante du code envoie une demande de billet le **completeAll** API sur le serveur principal :

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Ce formulaire est un appel de méthode typée et requiert que le type de retour **MarkAllResult** est défini. Méthodes tapées et non typées sont prises en charge.

##<a name="authentication"></a>Authentification des utilisateurs

Applications Mobile prend en charge l’authentification et l’autorisation des utilisateurs de l’application à l’aide de divers fournisseurs d’identité externes : Facebook, Google, Account Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables à limiter l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation scripts de serveur. Pour plus d’informations, consultez le didacticiel [authentification ajouter à votre application].

Deux flux d’authentification sont prises en charge : flux _gérés par le client_ et _géré par le serveur_ . Le flux géré par le serveur fournit l’expérience d’authentification la plus simple, car il repose sur l’interface du fournisseur web d’authentification. Intégration plus étroite avec des fonctionnalités spécifiques au périphérique permet du flux gérés par le client car il repose sur spécifiques du fournisseur spécifique à l’appareil SDK.

>[AZURE.NOTE] Nous vous recommandons d’utiliser un flux gérés par le client dans vos applications de production.

Pour configurer l’authentification, vous devez enregistrer votre application avec un ou plusieurs fournisseurs d’identité.  Le fournisseur d’identité génère un ID de client et un code secret client pour votre application.  Ces valeurs sont ensuite définies dans votre système principal pour activer le Service d’application Azure authentification/autorisation.  Pour plus d’informations, suivez les instructions détaillées dans le didacticiel [authentification ajouter à votre application].

Les rubriques suivantes sont décrites dans cette section :

+ [Authentification gérés par le client](#clientflow)
+ [Authentification géré par le serveur](#serverflow)
+ [La mise en cache le jeton d’authentification](#caching)

###<a name="clientflow"></a>Authentification gérés par le client

Votre application peut indépendamment contactez le fournisseur d’identité, puis fournissez le jeton retourné pendant la connexion avec votre serveur principal. Ce flux client vous permet de fournir une expérience d’authentification unique pour les utilisateurs ou pour extraire des données utilisateur supplémentaires à partir du fournisseur d’identité. Authentification du flux client est préférable d’utiliser un flux de serveur comme le fournisseur d’identité SDK fournit un aspect UX plus natif et permet de personnaliser.

Les exemples sont fournis pour les modèles d’authentification flux clients suivants :

+ [Bibliothèque d’authentification Active Directory](#adal)
+ [Facebook ou Google](#client-facebook)
+ [Le Live SDK](#client-livesdk)

#### <a name="adal"></a>Authentification des utilisateurs avec la bibliothèque d’authentification Active Directory

Vous pouvez utiliser la bibliothèque terme (Active Directory authentification ADAL) pour l’authentification des utilisateurs initier à partir du client à l’aide de l’authentification Azure Active Directory.

1. Configurer la principale application mobile pour l’AAD authentification en suivant le didacticiel [Comment faire pour configurer le Service d’application pour la connexion Active Directory] . Veillez à terminer l’étape facultative d’enregistrement d’une application cliente native.
2. Dans Visual Studio ou Xamarin Studio, ouvrez votre projet et ajoutez une référence à la `Microsoft.IdentityModel.CLients.ActiveDirectory` package NuGet. Lorsque vous recherchez, inclure les versions préliminaires.
3. Ajoutez le code suivant à votre application, en fonction de la plateforme que vous utilisez. Dans chacune, vérifiez les remplacements suivants :

    * Remplacez **Insertion-autorité-ici** par le nom du client dans lequel vous sa mise en service votre application. Le format doit être https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée à partir de l’onglet de domaine dans votre Azure Active Directory dans le [portail classique Azure].
    * Remplacez **Insertion-ressource-ID-ici** l’ID client pour la principale de l’application mobile. Vous pouvez obtenir l’ID de client à partir de l’onglet **Avancé** , sous **Paramètres Azure Active Directory** dans le portail.
    * Remplacez **Insertion-CLIENT-ID-ici** par l’ID de client que vous avez copiée à partir de l’application cliente native.
    * Remplacez **Insertion-redirection-URI-ici** par le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_.

    Le code requis pour chaque plate-forme suit :

    **Windows :**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>Authentification unique en utilisant un jeton de Facebook ou Google

Vous pouvez utiliser le flux des clients comme le montre cet extrait de code de Facebook ou Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>Seul se connecter à l’aide de Microsoft Account avec le Kit de développement Live

Pour l’authentification des utilisateurs, vous devez enregistrer votre application auprès du centre de développement de compte Microsoft. Configurez les détails de l’enregistrement sur votre serveur principal application Mobile. Pour créer un enregistrement compte Microsoft et vous connecter à votre serveur principal application Mobile, suivez les étapes de [Enregistrer votre application pour utiliser une connexion de compte Microsoft]. Si vous disposez du Windows Store et Windows Phone 8/Silverlight les versions de votre application, enregistrez tout d’abord la version du Windows Store.

Le code suivant authentifie à l’aide de Live SDK et utilise le jeton retourné pour se connecter à votre serveur principal application Mobile.

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Pour plus d’informations, consultez la documentation de [Windows Live SDK] .

###<a name="serverflow"></a>Authentification géré par le serveur

Une fois que vous avez enregistré votre fournisseur d’identité, appelez la méthode [LoginAsync] sur le [MobileServiceClient] avec la valeur [MobileServiceAuthenticationProvider] de votre fournisseur. Par exemple, le code suivant lance un serveur flux se connecter à l’aide de Facebook.

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Si vous utilisez un fournisseur d’identité autre que Facebook, définissez la valeur de [MobileServiceAuthenticationProvider] à la valeur de votre fournisseur.

Dans un flux de serveur, Azure Application Service gère le flux d’authentification OAuth en ouvrant la page de connexion du fournisseur sélectionné.  Une fois la renvoie fournisseur identité, Azure Application Service génère un jeton d’authentification de Service d’application. La méthode [LoginAsync] retourne un [MobileServiceUser], qui offre à la fois [nom d’utilisateur] de l’utilisateur authentifié et la [MobileServiceAuthenticationToken], en tant que JSON web jeton (JWT). Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu’il arrive à expiration. Pour plus d’informations, voir [mise en cache le jeton d’authentification](#caching).

###<a name="caching"></a>La mise en cache le jeton d’authentification

Dans certains cas, vous pouvez éviter l’appel à la méthode de connexion après la première authentification réussie en stockant le jeton d’authentification à partir du fournisseur.  Applications du Windows Store et UWP peuvent utiliser [PasswordVault] pour mettre en cache le jeton d’authentification Active après un réussi se connecter, comme suit :

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

La valeur nom d’utilisateur apparaît sous le nom d’utilisateur de l’information d’identification et stockées en tant que le mot de passe se trouve le jeton. Sous démarrage ultérieures, vous pouvez vérifier **PasswordVault** vos informations d’identification mises en cache. L’exemple suivant utilise les informations d’identification mises en cache lorsqu’ils sont trouvées, sinon essaie de s’authentifier à nouveau avec le serveur principal :

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Lorsque vous vous déconnectez un utilisateur, vous devez également supprimer les informations d’identification stockées, comme suit :

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Applications Xamarin utilisent l’API [Xamarin.Auth] pour stocker les informations d’identification en toute sécurité dans un objet de **compte** . Pour obtenir un exemple d’utilisation de ces API, voir le fichier de code [AuthStore.cs] dans la [photo ContosoMoments exemple de partage].

Lorsque vous utilisez l’authentification gérés par le client, vous pouvez également mettre en cache le jeton d’accès obtenu à partir de votre fournisseur comme Facebook ou Twitter. Ce jeton peut être fourni pour demander un nouveau jeton d’authentification à partir du serveur principal, comme suit :

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>Notifications de transmission

Les rubriques suivantes décrivent les Notifications de transmission :

* [S’inscrire pour les Notifications Push](#register-for-push)
* [Obtenir un identificateur de sécurité du package du Windows Store](#package-sid)
* [Enregistrer avec les modèles de disponibilité sur plusieurs plateformes](#register-xplat)

###<a name="register-for-push"></a>Comment : s’inscrire pour les Notifications Push

Le client applications Mobile vous permet de vous inscrire pour les notifications de transmission avec Hubs de Notification Azure. Lors de l’enregistrement, vous obtenez une poignée que vous obtenez à partir de spécifique à la plateforme transmission Notification Service (solution). Vous fournissez puis cette valeur ainsi que les balises lorsque vous créez l’enregistrement. Le code suivant enregistre votre application Windows pour les notifications push avec le Service de Notification de Windows (WNS) :

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Si vous s’effectue la diffusion WNS, vous devez [obtenir un identificateur de sécurité du package du Windows Store](#package-sid).  Pour plus d’informations sur les applications Windows, y compris comment enregistrer des enregistrements dans le modèle, voir [les notifications push ajouter dans votre application].

Demander des balises à partir du client n’est pas pris en charge.  Demandes de balise sont abandonnées silencieusement à partir de l’enregistrement.
Si vous souhaitez inscrire votre appareil avec indicateurs, créez une API personnalisée qui utilise l’API Hubs Notification pour effectuer l’inscription de votre part.  [Appeler l’API personnalisée](#customapi) au lieu de la `RegisterNativeAsync()` méthode.

###<a name="package-sid"></a>Comment : obtenir un identificateur de sécurité du package du Windows Store

Un identificateur de sécurité de package est nécessaire pour l’activation des notifications push dans les applications du Windows Store.  Pour recevoir un identificateur de sécurité du package, inscrivez votre application du Windows Store.

Pour obtenir cette valeur :

1. Dans Visual Studio Solution Explorer, clic droit sur le projet de l’application du Windows Store, cliquez sur **banque** > **Application associer avec le magasin...**.
2. Dans l’Assistant, cliquez sur **suivant**, connectez-vous avec votre compte Microsoft, tapez un nom pour votre application en **réserve un nouveau nom de l’application**, puis cliquez sur **réserver**.
3. Une fois l’inscription de l’application est créée, sélectionnez le nom de l’application et cliquez sur **suivant**, puis cliquez sur **associer**.
4. Connectez-vous au [Centre de développement Windows] à l’aide de votre Account Microsoft. Sous **Mes applications**, cliquez sur l’inscription de l’application que vous avez créé.
5. Cliquez sur **gestion de l’application** > **identité de l’application**et puis faites défiler vers le bas pour rechercher votre **Identificateur de Package de sécurité**.

Nombreuses utilisations du package identificateur de sécurité traitent sous forme d’URI, auquel cas vous devez utiliser _ms-application : / /_ comme modèle. Prenez note de la version de votre package d’identificateur de sécurité formé en concaténant cette valeur comme un préfixe.

Applications Xamarin nécessitent du code supplémentaire pour être en mesure d’enregistrer une application en cours d’exécution sur l’iOS ou Android plateformes. Pour plus d’informations, voir la rubrique relative à votre plateforme :

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Comment : modèles push de Registre pour envoyer des notifications de disponibilité sur plusieurs plateformes

Pour enregistrer les modèles, utilisez la `RegisterAsync()` méthode avec les modèles, comme suit :

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Vos modèles doivent être `JObject` types et peut contenir plusieurs modèles dans le format JSON suivant :

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

La méthode **RegisterAsync()** accepte également vignettes secondaires :

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Toutes les balises sont supprimés absent (e) lors de l’inscription pour la sécurité. Pour ajouter des indicateurs aux installations ou des modèles dans les installations, voir [fonctionnent avec le Kit de développement logiciel du serveur principal .NET pour les applications Mobile Azure].

Pour envoyer des notifications utilisant ces modèles enregistrés, reportez-vous à [Notification Hubs API].

##<a name="misc"></a>Rubriques diverses

###<a name="errors"></a>Comment : gérer les erreurs

En cas d’erreur dans le système principal, le client SDK génère une `MobileServiceInvalidOperationException`.  L’exemple suivant montre comment gérer une exception qui est retournée par le serveur principal :

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

Un autre exemple de traitement des conditions d’erreur sont accessibles dans l' [Exemple de fichiers d’applications mobiles]. L’exemple [LoggingHandler] fournit un gestionnaire de délégué journalisation (ci-dessous) pour vous connecter les demandes effectuées dans le système principal.

###<a name="headers"></a>Comment : personnaliser les en-têtes de requête

Pour prendre en charge votre scénario d’application spécifique, vous devrez peut-être personnaliser la communication avec le système principal de l’application Mobile. Par exemple, vous souhaitez ajouter un en-tête personnalisé à chaque demande sortante ou même en changeant les codes d’état de remise. Vous pouvez utiliser un personnalisé [DelegatingHandler], comme dans l’exemple suivant :

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Ajouter une authentification dans votre application]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[Ajouter les notifications push dans votre application]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Enregistrer votre application pour utiliser une connexion de compte Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment faire pour configurer le Service d’application pour connexion Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[Crée une référence à une table sans type]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Prendre]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Sélectionnez]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Ignorer]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[ID d’utilisateur]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Où]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Portail Azure]: https://portal.azure.com/
[Portail classique Azure]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centre de développement Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Kit de développement Windows Live]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exemple de fichiers applications mobiles]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentation présentée sous d’OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[Exemple de partage de photos ContosoMoments]: https://github.com/azure-appservice-samples/ContosoMoments