<properties
    pageTitle="Didacticiel de développement d’applications Java à l’aide de DocumentDB | Microsoft Azure"
    description="Ce didacticiel Java web application vous montre comment utiliser le service DocumentDB Azure pour stocker et accéder aux données à partir d’une application Java hébergée sur des sites Web Azure."
    keywords="Développement d’applications, didacticiel de base de données, application java, didacticiel de l’application web java, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter="java"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="08/24/2016"
    ms.author="denlee"/>

# <a name="build-a-java-web-application-using-documentdb"></a>Créer une application web Java à l’aide de DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

Ce didacticiel Java web application vous montre comment utiliser le service [Microsoft Azure DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB) pour stocker et accéder aux données à partir d’une application Java hébergée sur des sites Web Azure. Dans cette rubrique, vous allez apprendre :

- Découvrez comment créer une application de JSP base Eclipse.
- Comment travailler avec le service Azure DocumentDB en utilisant le [Kit de développement Java DocumentDB](https://github.com/Azure/azure-documentdb-java).

Ce didacticiel application Java vous montre comment créer une application de gestion des tâches sur le web qui permet de créer, récupérer et marquer les tâches comme terminé, comme le montre l’image suivante. Chacune des tâches dans la liste ToDo stockés en tant que documents JSON dans Azure DocumentDB.

![Mon application ToDo liste Java](./media/documentdb-java-application/image1.png)

> [AZURE.TIP] Ce didacticiel de développement application suppose que vous avez expérience antérieure à l’aide de Java. Si vous débutez dans Java ou les [Outils requis](#Prerequisites), nous vous recommandons de téléchargement du projet complet [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) à partir de GitHub et la création de celle-ci en suivant [les instructions à la fin de cet article](#GetProject). Une fois que vous avez créé, vous pouvez consulter l’article pour obtenir un aperçu du code dans le contexte du projet.  

##<a id="Prerequisites"></a>Conditions préalables pour ce didacticiel Java de l’application web
Avant de commencer ce didacticiel de développement d’applications, vous devez disposer des éléments suivants :

- Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- [Kit de développement Java (JDK) + 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
- [Eclipse IDE pour les développeurs Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
- [Un site Web Azure avec un environnement d’exécution Java (par exemple, Tomcat ou jetée) activé.](../app-service-web/web-sites-java-get-started.md)

Si vous installez ces outils pour la première fois, coreservlets.com fournit une vue d’ensemble du processus d’installation dans la section Démarrage rapide de leur [didacticiel : installation TomCat7 et son utilisation avec Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) article.

##<a id="CreateDB"></a>Étape 1 : Créer un compte de base de données DocumentDB

Nous allons commencer par créer un compte DocumentDB. Si vous possédez déjà un compte, vous pouvez passer à [étape 2 : créer l’application Java JSP](#CreateJSP).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

##<a id="CreateJSP"></a>Étape 2 : Créer l’application JSP Java

Pour créer l’application JSP :

1. Tout d’abord, nous allons commencer en créant un projet Java. Démarrer Eclipse, puis cliquez sur **fichier**, cliquez sur **Nouveau**, puis cliquez sur **Projet Web dynamique**. Si vous ne voyez pas **Dynamique Project Web** répertoriées sous forme d’un projet disponible, procédez comme suit : cliquez sur **fichier**, cliquez sur **Nouveau**, cliquez sur **le projet**..., développer **Web**, cliquez sur **Projet Web dynamique**et cliquez sur **suivant**.

    ![Développement d’applications JSP Java](./media/documentdb-java-application/image10.png)

2. Entrez un nom de projet dans la zone **nom du projet** , puis dans le menu déroulant **Runtime cible** , vous pouvez également sélectionner une valeur (par exemple, Apache Tomcat v7.0), puis cliquez sur **Terminer**. Sélection d’un runtime cible vous permet de se dérouler localement votre projet Eclipse.
3. Eclipse, dans l’affichage de l’Explorateur de projets, développez votre projet. Avec le bouton droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.
4. Dans la boîte de dialogue **Nouveau fichier JSP** , nommez fichier **index.jsp**. Conserver le dossier parent comme **contenu Web**, comme le montre l’illustration suivante, puis cliquez sur **suivant**.

    ![Créer un fichier JSP - Application Web Java didacticiel](./media/documentdb-java-application/image11.png)

5. Dans la boîte de dialogue **Sélectionner un modèle de JSP** pour ce didacticiel, sélectionnez **Nouveau fichier JSP (html)**et puis cliquez sur **Terminer**.

6. Lorsque le fichier index.jsp s’ouvre dans Eclipse, ajouter du texte à afficher **Bonjour !** dans l’objet existant <body> élément. Votre mise à jour <body> contenu doit se présenter comme le code suivant :

        <body>
            <% out.println("Hello World!"); %>
        </body>

8. Enregistrez le fichier index.jsp.
9. Si vous définissez un runtime cible à l’étape 2, vous pouvez cliquer sur **projet** , puis sur **exécuter** pour exécuter votre application JSP localement :

    ![Bonjour tout le monde – didacticiel d’Application Java](./media/documentdb-java-application/image12.png)

##<a id="InstallSDK"></a>Étape 3 : Installer le DocumentDB Java SDK ##

Pour extraire le Kit de développement Java DocumentDB et ses dépendances, le plus simple consiste à [Maven Apache](http://maven.apache.org/).

Pour ce faire, vous devez convertir votre projet à un projet maven en procédant comme suit :

1. Avec le bouton droit de votre projet dans l’Explorateur de projets, cliquez sur **configurer**, cliquez sur **convertir en Maven Project**.
2. Dans la fenêtre **Créer nouveau POM** , acceptez les paramètres par défaut et cliquez sur **Terminer**.
3. Dans l' **Explorateur de projets**, ouvrez le fichier pom.xml.
4. Sous l’onglet **dépendances** , dans le volet **dépendances** , cliquez sur **Ajouter**.
4. Dans la fenêtre **Sélectionnez dépendance** , procédez comme suit :
 - Dans la zone **GroupId** , entrez com.microsoft.azure.
 - Dans la zone **Objet Id** Entrez azure documentdb.
 - Dans la zone **Version** entrez 1.5.1.

    ![Installer l’Application DocumentDB Java SDK](./media/documentdb-java-application/image13.png)

    Ou ajoutez la dépendance XML GroupId et ArtifactId directement à la pom.xml via un éditeur de texte :

        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-documentdb</artifactId>
            <version>1.5.1</version>
        </dependency>

5. Cliquez sur **Ok** et Maven installe le Kit de développement Java DocumentDB.
6. Enregistrez le fichier pom.xml.

##<a id="UseService"></a>Étape 4 : Au moyen du service DocumentDB dans une application Java

1. Tout d’abord, nous allons définir l’objet TodoItem :

        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }

    Dans ce projet, nous utilisons [Lombok Project](http://projectlombok.org/) pour générer le constructeur, Get, méthodes Set et un générateur. Par ailleurs, vous pouvez écrire ce code manuellement ou que l’IDE générer.

2. Pour appeler le service DocumentDB, vous devez instanciation d’un nouveau **DocumentClient**. En règle générale, il est préférable de réutiliser le **DocumentClient** - plutôt que de créer un nouveau client pour chaque demande ultérieure. Nous pouvons réutiliser le client par le client à la ligne dans une **DocumentClientFactory**. C’est également l’endroit où vous avez besoin coller la valeur URI et clé primaire que vous avez enregistrés dans votre Presse-papiers à [l’étape 1](#CreateDB). Remplacez [votre\_point de terminaison\_ici] avec votre URI et le remplacer [votre\_clé\_ici] avec votre clé primaire.

        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";

        private static DocumentClient documentClient;

        public static DocumentClient getDocumentClient() {
            if (documentClient == null) {
                documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
            }

            return documentClient;
        }

3. Maintenant nous allons créer un objet DAO (Data Access) pour extraire la conservation notre tâches à DocumentDB.

    Pour enregistrer des tâches dans une collection de sites, le client doit connaître la base de données et la collection de sites pour conserver à (référencé par automatique des liens). En règle générale, il est préférable de mettre en cache la base de données et la collection de sites lorsque cela est possible afin d’éviter des boucles supplémentaires à la base de données.

    Le code suivant montre comment récupérer notre base de données et de la collection de sites, le cas échéant, ou pour créer un nouvel identifiant s’il n’existe :

        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";

            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";

            // The DocumentDB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();

            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;

            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;

            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();

                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);

                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }

                return databaseCache;
            }

            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();

                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);

                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }

                return collectionCache;
            }
        }

4. L’étape suivante consiste à écrire du code pour conserver la TodoItems dans à la collection. Dans cet exemple, nous utiliserons [Gson](https://code.google.com/p/google-gson/) sérialiser et pu vérifier TodoItem simple objets Java ancien (POJO) aux documents JSON. [Jackson](http://jackson.codehaus.org/) ou votre propre sérialiseur personnalisé sont également excellent alternatives pour sérialisation POJO.

        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();

        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));

            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");

            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }

            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }



5. Comme DocumentDB les bases de données et les collections de sites, les documents sont également référencées par automatique des liens. La fonction d’assistance suivante nous permet de récupérer des documents par un autre attribut (par exemple, « id ») plutôt que de créer un lien automatique :

        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();

            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }

6. Nous pouvons utiliser la méthode d’assistance à l’étape 5 pour récupérer un document TodoItem JSON par id, puis les désérialiser à un POJO :

        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);

            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }

7. Nous pouvons également utiliser la DocumentClient pour obtenir une collection de sites ou une liste de TodoItems à l’aide de DocumentDB SQL :

        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();

            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();

            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }

            return todoItems;
        }

8. Il existe de nombreuses façons de mettre à jour un document avec le DocumentClient. Dans notre application de liste Todo, nous voulons être en mesure d’afficher ou masquer un TodoItem est terminée. Ceci peut être réalisé en mettant à jour l’attribut « terminé » dans le document :

        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);

            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);

            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }

            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }

9. Pour finir, nous voulons la possibilité de supprimer un TodoItem de notre liste. Pour ce faire, nous pouvons utiliser la méthode d’assistance que nous avons rédigé précédemment pour récupérer la liaison automatique et puis indiquer au client pour la supprimer :

        @Override
        public boolean deleteTodoItem(String id) {
            // DocumentDB refers to documents by self link rather than id.

            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);

            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }

            return true;
        }


##<a id="Wire"></a>Étape 5 : Câblage le reste du projet de développement Java application gagnante

À présent que nous avons terminé l’amusant bits - tout cela gauche consiste à créer un utilisateur rapide de l’interface et fils à notre DAO.

1. Tout d’abord, nous allons commencer par créer un contrôleur pour appeler notre DAO :

        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }

            private static TodoItemController todoItemController;

            private final TodoDao todoDao;

            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }

            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }

            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }

            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }

            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }

            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }

    Dans une application plus complexe, le contrôleur peut héberger la logique métier complexe en haut de la DAO.

2. Ensuite, nous allons créer un servlet pour acheminer les requêtes HTTP au contrôleur :

        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";

            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";

            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";

            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";

            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();

            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {

                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;

                TodoItemController todoItemController = TodoItemController
                        .getInstance();

                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;

                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }

                response.getWriter().println(apiResponse);
            }

            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }

3. Nous avons besoin d’une Interface utilisateur Web à afficher à l’utilisateur. Nous allons réécriture la index.jsp que nous avons créé précédemment :

        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure DocumentDB Java Sample</title>

          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>

          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>

            <hr/>

            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>

              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>

            </div>

            <hr/>

            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>

                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>

                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>

          </div>

          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>

4. Et enfin, écrire du Javascript côté client pour relier l’interface utilisateur web et le servlet :

        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",

          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },

          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },

          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },

          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";

            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },

          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },

          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);

              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });

              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },

          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');

              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }

              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();

              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));

            });
          },

          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },

          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },

          ui_createButton: function() {
            return $(".todoForm button");
          },

          ui_table: function() {
            return $(".todoList table tbody");
          },

          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },

          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },

          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },

          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();

            todoApp.getTodoItems();
          }
        };

        $(document).ready(function() {
          todoApp.install();
        });

5. Isard ! Tout ce qui reste est désormais pour tester l’application. Exécutez l’application localement et ajoutez quelques tâches en remplissant le nom de l’élément et la catégorie et en cliquant sur **Ajouter une tâche**.

6. Une fois que l’élément s’affiche, vous pouvez mettre à jour si elle est terminée en activant la case à cocher en cliquant sur **Mettre à jour les tâches**.

##<a id="Deploy"></a>Étape 6 : Déployer votre application Java à des sites Web Azure

Sites Web Azure permet le déploiement d’Applications Java aussi simple que l’exportation de votre application comme un fichier WAR et soit téléchargeant via un contrôle de code source (par exemple, GIT) ou FTP.

1. Pour exporter votre application comme une guerre, avec le bouton droit sur votre projet dans **l’Explorateur de projets**, cliquez sur **Exporter**, puis cliquez sur **Fichier WAR**.
2. Dans la fenêtre **Guerre exporter** , procédez comme suit :
 - Dans la zone de project Web, entrez azure-documentdb-java-sample.
 - Dans la zone Destination, choisissez un emplacement où enregistrer le fichier WAR.
 - Cliquez sur **Terminer**.

3. Maintenant que vous avez un fichier WAR en main, vous pouvez simplement le télécharger sur répertoire **d’applications Web** du site Web de votre Azure. Pour obtenir des instructions sur le téléchargement du fichier, voir [Ajout d’une application à votre site Web Java sur Azure](../app-service-web/web-sites-java-add-app.md).

    Une fois que le fichier WAR est téléchargé dans le répertoire d’applications Web, l’environnement d’exécution détecte que vous avez ajouté et chargez automatiquement.
4. Pour afficher votre produit fini, accédez à http://YOUR\_SITE\_NAME.azurewebsites.net/azure-documentdb-java-sample/ et commencer à ajouter vos tâches !

##<a id="GetProject"></a>Obtenir le projet à partir de GitHub

Tous les exemples dans ce didacticiel sont inclus dans le projet [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) sur GitHub. Pour importer le projet todo dans Eclipse, assurez-vous que vous disposez du logiciel et les ressources répertoriées dans la section [conditions préalables](#Prerequisites) , puis procédez comme suit :

1. Installer [Project Lombok](http://projectlombok.org/). Lombok est utilisé pour générer des constructeurs, Get, méthodes Set dans le projet. Une fois que vous avez téléchargé le fichier lombok.jar, double-cliquez dessus pour l’installer ou l’installer à partir de la ligne de commande.
2. Si Eclipse est ouvert, fermez-le et redémarrez-le pour charger Lombok.
3. Dans Eclipse, dans le menu **fichier** , cliquez sur **Importer**.
4. Dans la fenêtre **Importer** , cliquez sur **Git**, cliquez sur **projets de Git**et puis cliquez sur **suivant**.
5. Dans l’écran **Sélectionner la Source de référentiel** , cliquez sur **Cloner URI**.
6. Dans l’écran **Référentiel Git Source** , dans la zone **URI** , entrez https://github.com/Azure-Samples/documentdb-java-todo-app.git, puis cliquez sur **suivant**.
7. Dans l’écran de **Sélection de branche** , assurez-vous que **maître** est sélectionnée, puis cliquez sur **suivant**.
8. Dans l’écran **Destination Local** , cliquez sur **Parcourir** pour sélectionner un dossier dans lequel le référentiel peut être copié, puis cliquez sur **suivant**.
9. Dans l’écran **Sélectionner un Assistant à utiliser pour importer des projets** , vérifiez que l’option **Importer des projets existants** est sélectionnée, puis cliquez sur **suivant**.
10. Dans l’écran **Projets importer** , désélectionner le projet **DocumentDB** , puis cliquez sur **Terminer**. Le projet DocumentDB contient le DocumentDB Java Kit de développement, nous allons ajouter en tant que dépendance à la place.
11. Dans l' **Explorateur de projets**, accédez à azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java et remplacez les valeurs hôte et MASTER_KEY avec l’URI et la clé primaire pour votre compte DocumentDB, puis enregistrez le fichier. Pour plus d’informations, voir [étape 1. Créer un compte de base de données DocumentDB](#CreateDB).
12. Dans l' **Explorateur de projets**, cliquez avec le bouton droit sur l' **exemple azure-documentdb-java**et cliquez sur **Créer un chemin d’accès**, puis cliquez sur **Configurer un chemin de créer**.
13. Dans l’écran **Le chemin d’accès de Java créer** , dans le volet droit, cliquez sur l’onglet **bibliothèques** , puis cliquez sur **Ajouter un JARs externes**. Accédez à l’emplacement du fichier lombok.jar et cliquez sur **Ouvrir**, puis cliquez sur **OK**.
14. Étape 12 permet d’ouvrir la fenêtre **Propriétés** à nouveau, puis dans le volet gauche, cliquez sur **Destinée aux exécutions**.
15. Dans l’écran **Destinée aux services d’exécution** , cliquez sur **Nouveau**et sélectionnez **Apache Tomcat v7.0**puis cliquez sur **OK**.
16. Étape 12 permet d’ouvrir la fenêtre **Propriétés** à nouveau, puis cliquez sur **Projet facettes**dans le volet gauche.
17. Dans l’écran **Facettes de projet** , sélectionnez **Module Web dynamique** et **Java**, puis cliquez sur **OK**.
18. Sous l’onglet **serveurs** dans la partie inférieure de l’écran, avec le bouton droit **Tomcat v7.0 serveur à l’hôte local** , puis sur **Ajouter et supprimer**.
19. Dans la fenêtre **Ajouter et supprimer** , atteindre **azure-documentdb-java-sample** la case **configuré** , puis cliquez sur **Terminer**.
20. Sous l’onglet **serveur** , avec le bouton droit **Tomcat v7.0 serveur à l’hôte local**, puis cliquez sur **redémarrer**.
21. Dans un navigateur, accédez à http://localhost : 8080/azure-documentdb-java-sample / et commencer à ajouter à votre liste de tâches. Notez que si vous avez modifié vos valeurs de port par défaut, modifier 8080 à la valeur que vous avez sélectionné.
22. Pour déployer votre projet sur un site web Azure, voir [étape 6. Déployer votre application aux sites Web Azure](#Deploy).

[1]: media/documentdb-java-application/keys.png
