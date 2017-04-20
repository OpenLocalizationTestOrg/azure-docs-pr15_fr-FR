<properties
    pageTitle="L’utilisation de la bibliothèque de Client applications Mobile Android"
    description="Comment utiliser Android client SDK for applications Mobile Azure."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>L’utilisation de la bibliothèque cliente Android pour les applications mobiles

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous montre comment utiliser le client Android SDK pour les applications mobiles pour mettre en œuvre des scénarios courants, tels que :

- Interrogation de données (insertion, mise à jour et suppression).
- Authentification.
- Gestion des erreurs.
- Personnalisation du client. 

Elle effectue également un approfondi en commun code client utilisé dans les applications mobiles plus.

Ce guide se concentre sur le Kit de développement Android côté client.  Pour en savoir plus sur les kits de développement côté serveur pour les applications mobiles, voir [collaborer avec le serveur principal .NET SDK] [ 10] ou [comment utiliser le système principal Node.js SDK][11].

## <a name="reference-documentation"></a>Documentation de référence

Vous pouvez trouver la [référence de l’API Javadocs] [ 12] pour la bibliothèque cliente Android sur GitHub.

## <a name="supported-platforms"></a>Plateformes prises en charge

Le Kit de développement de Azure Mobile applications Android prend en charge les niveaux de l’API 19 à 24 (KitKat via la commande).  

L’authentification « flux de serveur » utilise un mode d’affichage Web pour l’interface utilisateur présentée. Si l’appareil n’est pas en mesure de présenter un WebView UI, d’autres méthodes d’authentification sont nécessaires qui se trouve en dehors de l’étendue du produit.  Ce SDK n’est pas adapté à type d’espion ou de la même manière des périphériques restreints.

## <a name="setup-and-prerequisites"></a>Le programme d’installation et configuration requise

Utiliser le didacticiel de [démarrage rapide d’applications Mobile](app-service-mobile-android-get-started.md) .  Grâce à cette tâche, toutes les conditions préalables pour le développement d’applications Mobile Azure ont été satisfaites.  Démarrage rapide vous permet également de configurer votre compte et créer la première principale de l’application Mobile.

Si vous décidez de ne pas utiliser le didacticiel de démarrage rapide, procédez comme suit :

- [créer une application Mobile principale] [ 13] à utiliser avec votre application Android.
- Dans Studio Android, [mettre à jour les fichiers de build Gradle](#gradle-build).
- [Activer les autorisations internet](#enable-internet).

###<a name="gradle-build"></a>Mettre à jour le fichier de génération Gradle

Modifier les deux documents **build.gradle** :

1. Ajoutez ce code au fichier de niveau **build.gradle** du *projet* à l’intérieur de la balise *buildscript* :

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Ajoutez ce code dans le fichier de niveau **build.gradle** *Module application* à l’intérieur de la balise de *dépendances* :

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    La version la plus récente n’existe actuellement 3.1.0. Les versions prises en charge sont répertoriées [ici][14].

###<a name="enable-internet"></a>Activer les autorisations internet

Pour accéder à Azure, votre application doit avoir l’autorisation d’INTERNET. Si ce n’est pas déjà fait, ajoutez la ligne suivante du code à votre fichier **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>Le concepts de base approfondie

Cette section décrit certaines lignes de code dans l’application de démarrage rapide qui décrit l’utilisation des applications Mobile Azure.  

###<a name="data-object"></a>Définir des classes de données client

Pour accéder aux données de tables SQL Azure, définir des classes de données client qui correspondent aux tables dans le système principal de l’application Mobile. Exemples de cette rubrique part du principe une table nommée **ToDoItem**qui comporte les colonnes suivantes :

- ID
- texte
- Terminer

Le correspondant tapé objet côté client :

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

Le code réside dans un fichier nommé **ToDoItem.java**.

Si votre table SQL Azure contient plus de colonnes, vous ajouterez les champs correspondants à ce cours.  Par exemple, si le DTO (objet de transfert de données) a une colonne de priorité entier, puis vous pouvez ajouter ce champ, ainsi que ses méthodes de lecture et :

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Pour découvrir comment créer des tables supplémentaires dans votre système principal applications Mobile, voir [Comment : définir un contrôleur de tableau] [ 15] (serveur principal .NET) ou [définir des Tables à l’aide d’un schéma dynamique] [ 16] (Node.js principal). Pour un serveur principal Node.js, vous pouvez également utiliser le paramètre de **tables faciles** dans le [portail Azure].

###<a name="create-client"></a>Comment : créer le contexte client

Ce code crée l’objet **MobileServiceClient** qui est utilisé pour accéder à votre serveur principal application Mobile. Le code est inséré la `onCreate` méthode de la classe **activité** spécifiée dans *AndroidManifest.xml* une action **principale** et une catégorie de **Lanceur d’applications** . Dans le code de démarrage rapide, il se trouvent dans le fichier **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

Dans ce code, remplacez `MobileAppUrl` avec l’URL de votre serveur principal application Mobile, qui se trouve dans le [portail Azure] dans la carte pour la principale de l’application Mobile. Pour cette ligne de code à compiler, vous devez également ajouter l’instruction **import** suivante :

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Comment : créer une référence de table

Pour interroger ou modifier des données dans le système principal, le plus simple consiste à l’aide de *tapé le modèle de programmation*, dans la mesure où Java est un langage fortement typé. Ce modèle fournit transparente JSON sérialisation et désérialisation à l’aide de la [gson] [ 3] bibliothèque lors de l’envoi des données entre les tables et les objets du client dans le système principal SQL Azure.

Pour accéder à une table, commencez par créer une [MobileServiceTable] [ 8] objet en appelant le **getTable** méthode sur le [MobileServiceClient][9].  Cette méthode implique deux surcharges :

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

Dans le code suivant, **mClient** est une référence à votre objet MobileServiceClient.  La première surcharge est utilisée à l’endroit où le nom du cours et le nom de la table sont les mêmes, et est celui utilisé dans le démarrage rapide :

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

La deuxième surcharge est utilisée lorsque le nom de table est différent du nom du cours : le premier paramètre est le nom de table.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Comment : lier des données à l’interface utilisateur

Liaison de données implique trois composants :

- La source de données
- Disposition de l’écran
- La carte qui relie les deux.

Dans notre exemple, nous retourner les données de la table Mobile applications SQL Azure **ToDoItem** dans un tableau. Cette activité est un modèle courant pour les applications de données.  Requêtes de base de données retournent souvent une collection de lignes qui tire le client dans une liste ou un tableau. Dans cet exemple, le tableau est la source de données.

Le code spécifie une disposition de l’écran qui définit la vue des données qui s’affiche sur l’appareil.  Les deux sont liés avec une carte, qui dans ce code est une extension de la **ArrayAdapter&lt;ToDoItem&gt; ** cours.

#### <a name="layout"></a>Comment : définir la mise en page

La mise en page est défini par plusieurs extraits de code XML. Étant donné une disposition existante, le code suivant représente la **liste** nous voulons pour remplir avec nos données serveur.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

Dans le code précédent, l’attribut *listitem* Spécifie l’id de la disposition d’une ligne dans la liste. Ce code indique une case à cocher et son texte associé et obtient instanciation une seule fois pour chaque élément dans la liste. Cette disposition n’affiche pas le champ **id** et une mise en page plus complexe spécifierez des champs supplémentaires dans l’affichage. Ce code se trouve dans le fichier **row_list_to_do.xml** .

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>Comment : définir la carte

Étant donné que la source de données de notre vue est un tableau de **ToDoItem**, nous sous-classe notre adaptateur à partir d’un **ArrayAdapter&lt;ToDoItem&gt; ** cours. Cette sous-classe fournit une vue pour chaque **ToDoItem** à l’aide de la mise en page **row_list_to_do** .

Dans notre code nous définir la classe suivante est une extension de la **ArrayAdapter&lt;E&gt; ** cours :

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Remplacer la méthode **getView** cartes. Par exemple :

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Nous créons une instance de cette classe dans notre activité comme suit :

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Le deuxième paramètre au constructeur ToDoItemAdapter est une référence à la mise en page. Nous pouvons maintenant instanciation **liste** et affecter la carte à la **liste**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>La structure de l’API

Opérations de table applications Mobile et les appels API personnalisés sont asynchrones. Utiliser les objets [Future] et [AsyncTask] pour les méthodes asynchrones impliquant des requêtes, insertions, mises à jour et suppressions. Début au plus tôt rend plus facile d’effectuer plusieurs opérations sur un thread d’arrière-plan sans avoir à gérer plusieurs rappels imbriquées.

Passez en revue le fichier **ToDoActivity.java** dans le projet de démarrage rapide pour Android à partir du [portail Azure] pour obtenir un exemple.

#### <a name="use-adapter"></a>Comment : utiliser la carte

Vous êtes maintenant prêt à utiliser la liaison de données. Le code suivant montre comment obtenir des éléments dans la table et remplit la carte locale avec les éléments retournés.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Appeler la carte chaque fois que vous modifiez la table **ToDoItem** . Dans la mesure où les modifications sont effectuées par un enregistrement, vous gérez une seule ligne au lieu d’une collection de sites. Lorsque vous insérez un élément, appelez la méthode **add** sur la carte ; lors de la suppression, appelez la méthode **remove** .

##<a name="querying"></a>Comment : interroger des données à partir de votre serveur principal application Mobile

Cette section décrit comment émettre des requêtes sur le serveur principal application Mobile, qui inclut les tâches suivantes :

- [Retourner tous les éléments]
- [Filtrer les données renvoyées]
- [Tri renvoyé des données]
- [Renvoyer les données dans des pages]
- [Sélectionner des colonnes spécifiques]
- [Méthodes de requête CONCATENER](#chaining)

### <a name="showAll"></a>Comment : retourner tous les éléments d’une Table

La requête suivante renvoie tous les éléments dans la table **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

La variable de *résultats* retourne les résultats de la requête sous forme de liste.

### <a name="filtering"></a>Comment : filtrer les données renvoyées

L’exécution de la requête suivante renvoie tous les éléments de la table **ToDoItem** où **achevé** est égal à **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** est la référence à la table de service mobile que nous avons créé précédemment.

Définissez un filtre à l’aide de l’appel de méthode **où** sur la référence de table. La méthode **where** est suivie d’une méthode de **champ** suivie d’une méthode qui spécifie le prédicat logique. Méthodes prédicats possibles incluent **eq** (signe égal), **garanties** (non égal), **gt** (supérieur à), **page** (supérieur ou égal à), **lt** (inférieur à), **le** (inférieur ou égal à). Ces méthodes vous permettent de comparer les champs numériques et de chaîne à des valeurs spécifiques.

Vous pouvez filtrer les dates. Les méthodes suivantes vous permettent de comparer le champ date entière ou la date : **année**, **mois**, **jour**, **heure**, **minute**et **seconde**. L’exemple suivant ajoute un filtre pour les éléments dont la *date d’échéance* est égal à 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Les méthodes suivantes prennent en charge des filtres complexes sur les champs de type chaîne : **startsWith**, **endsWith**, **concat**, **sous-chaîne**, **indexOf**, **Remplacer**, **toLower**, **toUpper**, **découper**et **longueur**. L’exemple suivant filtre pour les lignes de la table dont la colonne de *texte* commence par « PRI0 ».

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Les méthodes d’opérateur suivants sont pris en charge dans les champs numériques : **Ajouter**, **sub**, **mul**, **div**, **mod**, **plancher**, **plafond**et **arrondir**. L’exemple suivant des filtres pour les lignes de la table dans laquelle la **durée** est un nombre pair.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Vous pouvez combiner prédicats avec ces méthodes logiques : **et**, **ou** et **pas**. L’exemple suivant combine deux des exemples précédents.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Regrouper et imbriquer des opérateurs logiques :

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Pour plus d’informations et des exemples de filtrage, consultez la rubrique [présentation la richesse du modèle requête client Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Comment : trier les données renvoyées

Le code suivant renvoie que tous les éléments d’un tableau de **ToDoItems** trié par ordre croissant par le champ de *texte* . *mToDoTable* est la référence à la table et que vous avez créé précédemment :

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Le premier paramètre de la méthode **orderBy** est une chaîne correspondant au nom du champ sur lequel effectuer le tri. Le second paramètre utilise l’énumération **QueryOrder** pour spécifier si vous souhaitez trier par ordre croissant ou décroissant.  Si vous filtrez à l’aide de la méthode ***where*** , la méthode ***where*** doit être appelée avant la méthode ***orderBy*** .

### <a name="paging"></a>Comment : retourner des données dans des pages

Le premier exemple indique comment sélectionner les cinq premiers éléments d’un tableau. La requête renvoie les éléments d’un tableau de **ToDoItems**. **mToDoTable** est la référence à la table et que vous avez créé précédemment :

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Voici une requête qui permet d’ignorer les cinq premiers éléments, puis renvoie les cinq suivantes :

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Comment : sélectionner des colonnes spécifiques

Le code suivant montre comment retourner tous les éléments à partir d’une table des **ToDoItems**, mais n’affiche que les champs **complète** et le **texte** . **mToDoTable** est la référence à la table principale que nous avons créé précédemment.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Les paramètres de la fonction select sont les noms de chaîne des colonnes de la table dont vous souhaitez rétablir.

La méthode **select** doit suivre méthodes, telles que **l’emplacement** et **orderBy**. Il peut être suivi par méthodes de pagination comme **haut**.

### <a name="chaining"></a>Comment : concaténer des méthodes de requête

Les méthodes utilisées dans l’interrogation et tables peuvent être concaténées. Chaîne méthodes de requête permet de sélectionner des colonnes spécifiques de lignes filtrées qui sont triés et la pagination. Vous pouvez créer des filtres logiques complexes.
Chaque méthode requête retourne un objet de la requête. Pour mettre fin à la série de méthodes et exécuter la requête, appelez la méthode **exécuter** . Par exemple :

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Les méthodes de chaîne de requête doivent être classées comme suit :

1. Méthodes de filtrage (**où**).
2. Méthodes de tri (**orderBy**).
3. Méthodes de sélection (**Sélectionnez**).
4. méthodes (**Ignorer** et **haut**) de pagination.

##<a name="inserting"></a>Comment : insérer des données dans le système principal

Créer une instance de la classe *ToDoItem* et définissez ses propriétés.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Utilisez ensuite **insert()** pour insérer un objet :

    ToDoItem entity = mToDoTable.insert(item).get();

Les correspondances entité retournés les données insérées dans la table principale, inclus l’ID et toutes les autres valeurs définies sur le serveur principal.

Les tables applications Mobile nécessitent une colonne de clé primaire intitulée **id**. Par défaut, cette colonne est une chaîne. La valeur par défaut de la colonne ID est un GUID.  Vous pouvez fournir d’autres valeurs uniques, tels que les adresses de messagerie ou les noms d’utilisateur. Lorsqu’une valeur d’ID de chaîne n’est pas fournie pour un enregistrement inséré, le système principal génère un nouveau GUID.

Valeurs des ID de chaîne présentent les avantages suivants :

+ ID peuvent être générés sans modifier accidentellement un aller et retour à la base de données.
+ Enregistrements sont plus faciles à utiliser lors de différentes tables ou des bases de données de fusion.
+ Valeurs d’ID s’intègrent mieux logique d’une application.

Valeurs d’ID de chaîne sont **requis** pour la prise en charge de la synchronisation hors connexion.

##<a name="updating"></a>Comment : mettre à jour les données dans une application mobile

Pour mettre à jour des données dans un tableau, passez le nouvel objet à la méthode **Update ()** .

    mToDoTable.update(item).get();

Dans cet exemple, *élément* est une référence à une ligne dans la table *ToDoItem* , qui a été certaines modifications apportées à celui-ci.
La ligne avec le même **id** est mis à jour.

##<a name="deleting"></a>Comment : supprimer des données dans une application mobile

Le code suivant montre comment supprimer des données d’un tableau en spécifiant l’objet de données.

    mToDoTable.delete(item);

Vous pouvez également supprimer un élément en spécifiant le champ **id** de la ligne à supprimer.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>Comment : rechercher un élément spécifique

Rechercher un élément avec un champ spécifique **id** avec la méthode **lookUp()** :

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>Comment : utiliser des données non typées

Le modèle de programmation non typé vous permet de contrôler exacte de sérialisation JSON.  Il existe quelques scénarios courants où vous souhaitez utiliser un modèle de programmation non typé. Par exemple, si votre tableau principal contient beaucoup de colonnes et vous n’avez besoin de faire référence à un sous-ensemble des colonnes.  Le modèle tapé, vous devez définir toutes les applications mobiles colonnes de la table dans votre cours de données.  

La plupart des appels API d’accès aux données est similaire aux appels de programmation tapées. La différence principale est que dans le modèle non typé vous appelez des méthodes sur l’objet **MobileServiceJsonTable** , au lieu de l’objet **MobileServiceTable** .

### <a name="json_instance"></a>Comment : créer une instance d’une table sans type

Semblable au modèle tapé, vous démarrez en obtenant une référence de table, mais dans ce cas, il est un objet **MobileServicesJsonTable** . Obtenir la référence en appelant le **getTable** méthode sur une instance du client :

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Une fois que vous avez créé une instance de l' **MobileServiceJsonTable**, il a pratiquement la même API disponible en tant qu’avec le modèle de programmation tapé. Dans certains cas, les méthodes prennent un paramètre non typé au lieu d’un paramètre tapé.

### <a name="json_insert"></a>Comment : insérer dans une table sans type

Le code suivant montre comment effectuer une opération d’insertion. La première étape consiste à créer un [JsonObject][1], qui fait partie de la [gson] [ 3] bibliothèque.

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Ensuite, utilisez **insert()** pour insérer l’objet non typée dans la table.

    mJsonToDoTable.insert(jsonItem).get();

Si vous avez besoin obtenir l’ID de l’objet inséré, utilisez la méthode **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Comment : supprimer à partir d’une table sans type

Le code suivant montre comment supprimer une instance, dans ce cas, la même instance d’un **JsonObject** qui a été créé dans l’exemple précédent *Insérer* . Le code est identique à la casse tapée, mais la méthode a une signature différente dans la mesure où elle fait référence à un **JsonObject**.

         mToDoTable.delete(jsonItem);

Vous pouvez également supprimer une instance directement à l’aide de son ID :

         mToDoTable.delete(ID);

### <a name="json_get"></a>Comment : renvoyer toutes les lignes d’une table sans type

Le code suivant montre comment extraire une table entière. Dans la mesure où vous utilisez une Table JSON, vous pouvez extraire uniquement une partie des colonnes de la table de manière sélective.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Le même jeu de filtrage, filtrage et la pagination méthodes sont disponibles pour le modèle tapé sont disponibles pour le modèle de non typé.

##<a name="custom-api"></a>Comment : appeler une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison personnalisés qui exposent les fonctionnalités du serveur qui ne pas établir une correspondance entre une instruction insert, mettre à jour, supprimer ou opération de lecture. En utilisant une API personnalisée, vous pouvez avoir plus de contrôle sur la messagerie, notamment la lecture et définir des en-têtes de message HTTP et définir un format de corps du message que JSON.

À partir d’un client Android, vous appelez la méthode **invokeApi** pour appeler le point de terminaison API personnalisé. L’exemple suivant montre comment appeler un point de terminaison API nommé **completeAll**, qui retourne une classe collection nommée **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

La méthode **invokeApi** est appelée sur le client, qui envoie une demande de publication à la nouvelle API personnalisée. Le résultat retourné par l’API personnalisé s’affiche dans une boîte de dialogue, comme des erreurs se produisent. Autres versions de **invokeApi** vous permettent de vous pouvez également envoyer un objet dans le corps de la requête, spécifier la méthode HTTP et envoyer des paramètres de la requête avec la demande. Non typées versions **d’invokeApi** sont également fournies.

##<a name="authentication"></a>Comment : ajouter une authentification dans votre application

Didacticiels décrivent déjà en détail comment ajouter ces fonctionnalités.

Service d’application prend en charge [l’authentification des utilisateurs de l’application](app-service-mobile-android-get-started-users.md) à l’aide d’une variété de fournisseurs d’identité externes : Facebook, Google, Account Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables à limiter l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation dans votre système principal.

Deux flux d’authentification sont prises en charge : un flux de **serveur** et un flux de **client** . Le flux server fournit l’expérience d’authentification la plus simple, car elle se base sur l’interface identité fournisseurs web.  Aucun SDK supplémentaires n’est nécessaires pour implémenter l’authentification serveur flux. Authentification de flux Server ne fournit pas une intégration dans l’appareil mobile et est recommandée uniquement pour la preuve de scénarios concept.

Intégration plus étroite avec des fonctionnalités spécifiques au périphérique tels que de l’authentification unique permet du flux du client car il repose sur SDK fourni par le fournisseur d’identité.  Par exemple, vous pouvez intégrer le SDK Facebook dans votre application mobile.  Le client mobile permutations dans l’application Facebook et confirme votre authentification avant inversion revenir à votre application mobile.

Quatre étapes sont nécessaires pour activer l’authentification dans votre application :

- Enregistrer votre application pour l’authentification avec un fournisseur d’identité.
- Configurer votre version de serveur de Service d’application.
- Restreindre les autorisations de table pour les utilisateurs authentifiés uniquement sur le serveur de Service d’application principal.
- Ajouter le code d’authentification dans votre application.

Vous pouvez définir des autorisations sur les tables à limiter l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identificateur de sécurité d’un utilisateur authentifié modifier des requêtes.  Pour plus d’informations, consultez [prise en main d’authentification] et la documentation de faire du Kit de développement logiciel serveur.

### <a name="caching"></a>Comment : ajouter le code d’authentification dans votre application

Le code suivant démarre un processus de connexion de flux de serveur en utilisant le fournisseur de Google :

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obtenir l’ID de l’utilisateur connecté à partir d’un **MobileServiceUser** à l’aide de la méthode **getUserId** . Pour obtenir un exemple d’utilisation des perspectives pour appeler la connexion asynchrone API, voir [prise en main d’authentification].

### <a name="caching"></a>Comment : mettre en Cache les jetons d’authentification

Mise en cache des jetons d’authentification, vous devez stocker localement les ID d’utilisateur et le jeton d’authentification sur l’appareil. La prochaine fois que l’application démarre, vous consultez le cache et si ces valeurs sont présents, vous pouvez ignorer le journal de procédure et réalimenter le client avec ces données. Toutefois ces données sont sensibles, et il doit être stocké chiffré pour la sécurité au cas où le téléphone obtient vol.

Vous pouvez obtenir un exemple de façon à jetons d’authentification de cache dans la [section de Cache d’authentification jetons][7].

Lorsque vous essayez d’utiliser un jeton qui a expiré, vous recevez une réponse *401 non autorisé* . Vous pouvez gérer les erreurs d’authentification à l’aide de filtres.  Filtres interceptent des demandes sur le serveur principal de Service d’application. Le code de filtre teste la réponse pour un 401, déclenche le processus de connexion, puis reprend la demande ayant généré le 401. 

## <a name="adal"></a>Comment : authentifier les utilisateurs avec la bibliothèque d’authentification Active Directory

Vous pouvez utiliser la bibliothèque terme (Active Directory authentification ADAL) pour vous connecter les utilisateurs à votre application à l’aide d’Azure Active Directory. À l’aide d’une connexion de flux du client est souvent préférable à l’aide de la `loginAsync()` méthodes tel qu’il fournit un aspect UX plus natif et permet de personnaliser.

1. Configurer la principale application mobile pour se connecter AAD en suivant le didacticiel [Comment faire pour configurer le Service d’application pour la connexion Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Veillez à terminer l’étape facultative d’enregistrement d’une application cliente native.

2. Installer le terme ADAL en modifiant votre fichier build.gradle pour inclure les définitions suivantes :

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Ajouter le code suivant à votre application, qui effectue les remplacements suivants :

* Remplacez **Insertion-autorité-ici** par le nom du client dans lequel vous sa mise en service votre application. Le format doit être https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée à partir de l’onglet de domaine dans votre Azure Active Directory [portail classique Azure].

* Remplacez **Insertion-ressource-ID-ici** l’ID client pour la principale de l’application mobile. Vous pouvez obtenir l’ID de client à partir de l’onglet **Avancé** , sous **Paramètres Azure Active Directory** dans le portail.

* Remplacez **Insertion-CLIENT-ID-ici** par l’ID de client que vous avez copiée à partir de l’application cliente native.

* Remplacez **Insertion-redirection-URI-ici** par le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>Comment : ajouter une notification push à votre application

Vous pouvez [lire une présentation] [ 6] qui décrit comment Microsoft Azure Notification Hubs prend en charge un large éventail de notifications de transmission.  Dans [ce didacticiel][5], une notification push est envoyée à tous les périphériques chaque fois qu’un enregistrement est inséré.

## <a name="how-to-add-offline-sync-to-your-app"></a>Comment : ajoutez la synchronisation hors connexion dans votre application

Le didacticiel de démarrage rapide contient code qui mettent en œuvre, synchronisation hors connexion. Recherchez le code le préfixe des commentaires, voir :

    // Offline Sync

Commentaire de lignes de code suivantes vous pouvez d’implémenter synchronisation hors connexion, et vous pouvez ajouter du code semblable à un autre code d’applications Mobile.

##<a name="customizing"></a>Comment : personnaliser le client

Il existe plusieurs méthodes vous permettant de personnaliser le comportement par défaut du client.

### <a name="headers"></a>Comment : personnaliser les en-têtes de requête

Configurer un **ServiceFilter** pour ajouter un en-tête HTTP personnalisé à chaque demande :

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Comment : personnaliser la sérialisation

Le client suppose que les noms des tables, les noms de colonne et données tape sur le serveur principal toutes correspondent exactement les objets de données définis dans le client. Il peut être un nombre quelconque de raisons pourquoi les noms de client et serveur peuvent ne pas correspondent. Dans votre scénario, vous souhaiterez peut-être effectuer les types de personnalisations suivants :

- Les noms de colonne utilisés dans la table de services d’application ne correspondent pas les noms que vous utilisez dans le client.
- Utiliser un tableau de Service d’application qui comporte un nom différent de celui la classe que correspondant dans le client.
- Activer la mise en majuscule automatique des propriétés.
- Ajouter des propriétés complexes à un objet.

### <a name="columns"></a>Comment : mapper des noms de client et serveur différents

Supposons que votre code client Java utilise les noms de style Java standard pour les propriétés d’objet **ToDoItem** , telles que les propriétés suivantes :

- STXT
- mText
- mComplete
- fonction DUREE

Sérialiser les noms de client en noms JSON qui correspondent aux noms de colonne de la table **ToDoItem** sur le serveur. Le code suivant utilise la [gson] [ 3] bibliothèque à annoter les propriétés :

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Comment : mapper les noms de tables différentes entre le client et le serveur principal

Établir une correspondance entre le nom de la table client un nom de table différents services mobile à l’aide d’une substitution de la [getTable()] [ 4] méthode :

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Comment : automatiser les mappages de nom de colonne

Vous pouvez spécifier une stratégie de conversion qui s’applique à toutes les colonnes à l’aide de la [gson] [ 3] API. La bibliothèque cliente Android utilise [gson] [ 3] en coulisses pour sérialiser des objets Java aux données JSON avant que les données sont envoyées au Service d’application Azure.  Le code suivant utilise la méthode **setFieldNamingStrategy()** pour définir la stratégie. Cet exemple montre comment supprimer le caractère initial (un « m ») et en minuscules puis le caractère suivant, pour chaque nom de champ. Par exemple, il serait transformer « mId » en « id ».

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Ce code doit être exécuté avant d’utiliser la **MobileServiceClient**.

### <a name="complex"></a>Comment : stocker une propriété objet ou un tableau dans une table

Jusque là, nos exemples sérialisation ont impliquées types primitifs tels que des entiers et des chaînes.  Types primitifs sérialiser facilement en JSON.  Si nous voulons ajouter un objet complexe qui ne sérialiser automatiquement à JSON, nous avons besoin de fournir la méthode de sérialisation JSON.  Pour afficher un exemple montrant comment fournir une sérialisation JSON personnalisée, consultez le billet de blog [sérialisation personnalisation à l’aide de la bibliothèque gson dans le client Mobile Services Android][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Retourner tous les éléments]: #showAll
[Filtrer les données renvoyées]: #filtering
[Tri renvoyé des données]: #sorting
[Renvoyer les données dans des pages]: #paging
[Sélectionner des colonnes spécifiques]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portail Azure]: https://portal.azure.com
[Prise en main d’authentification]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Avenir]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html