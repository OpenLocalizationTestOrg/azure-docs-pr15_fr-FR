<properties 
    pageTitle="Créer une application web dans Azure qui se connecte à MongoDB en cours d’exécution sur une machine virtuelle" 
    description="Un didacticiel qui vous apprend à utiliser Git pour déployer une application ASP.NET au Service d’application Azure, connecté à MongoDB sur une Machine virtuelle Azure."
    tags="azure-portal" 
    services="app-service\web, virtual-machines" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/29/2016" 
    ms.author="cephalin"/>


# <a name="create-a-web-app-in-azure-that-connects-to-mongodb-running-on-a-virtual-machine"></a>Créer une application web dans Azure qui se connecte à MongoDB en cours d’exécution sur une machine virtuelle

À l’aide de Git, vous pouvez déployer une application ASP.NET à Azure Application Service Web Apps. Dans ce didacticiel, vous allez générer un simple frontale ASP.NET MVC application de liste de tâches qui se connecte à une base de données MongoDB en cours d’exécution sur une machine virtuelle dans Azure.  [MongoDB] [ MongoDB] est une base de données de haute performance NoSQL populaires ouvrir la source. Après avoir en cours d’exécution et test de l’application ASP.NET sur votre ordinateur de développement, vous allez télécharger l’application à l’aide Git application Service Web Apps.

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.


## <a name="background-knowledge"></a>Notions de base ##

Connaissance des opérations suivantes est utile pour ce didacticiel, mais ne pas obligatoire :

* Le pilote c# pour MongoDB. Pour plus d’informations sur le développement d’applications c# contre MongoDB, voir le MongoDB [CSharp langue centre][MongoC#LangCenter]. 
* L’infrastructure d’application web ASP.NET. Vous pouvez pour tout savoir sur le [site Web ASP.net][ASP.NET].
* L’infrastructure d’application web ASP .NET MVC. Vous pouvez pour tout savoir sur le [site Web ASP.NET MVC][MVCWebSite].
* Azure. Vous pouvez commencer en train de lire en [Azure][WindowsAzure].

## <a name="prerequisites"></a>Conditions préalables ##

- [Visual Studio Express 2013 pour Web]  [ VSEWeb] ou [Visual Studio 2013] [VSUlt]
- [Kit de développement Azure pour .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Un abonnement actif à Microsoft Azure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a> 
## <a name="create-a-virtual-machine-and-install-mongodb"></a>Créer une machine virtuelle et installer MongoDB ##

Ce didacticiel suppose que vous avez créé une machine virtuelle dans Azure. Après avoir créé la machine virtuelle, vous devez installer MongoDB sur la machine virtuelle :

* Pour créer une machine virtuelle Windows et installer MongoDB, voir [Installer MongoDB sur une machine virtuelle exécutant Windows Server dans Azure][InstallMongoOnWindowsVM].


Une fois que vous avez créé la machine virtuelle dans Azure et installé MongoDB, veillez à mémoriser le nom DNS de la machine virtuelle (« testlinuxvm.cloudapp.net », par exemple) et le port externe pour MongoDB que vous avez spécifié dans le point de terminaison.  Vous avez besoin de ces informations plus loin dans le didacticiel.

<a id="createapp"></a>
## <a name="create-the-application"></a>Créer l’application ##

Dans cette section, vous créez une application ASP.NET appelée « Ma liste de tâches » à l’aide de Visual Studio et effectuer un déploiement initial à Azure Application Service Web Apps. Vous exécuterez l’application localement, mais il se connecter à votre machine virtuelle sur Azure et utilisez l’instance MongoDB que vous avez créé.

1. Dans Visual Studio, cliquez sur **Nouveau projet**.

    ![Démarrez Page Nouveau projet][StartPageNewProject]

1. Dans la fenêtre **Nouveau projet** , dans le volet gauche, sélectionnez **Visual c#**, puis **Web**. Dans le volet central, sélectionnez **Application Web ASP.NET**. Dans la partie inférieure, nommez votre projet « MyTaskListApp », puis cliquez sur **OK**.

    ![Boîte de dialogue Nouveau projet][NewProjectMyTaskListApp]

1. Dans la boîte de dialogue **Nouveau projet ASP.NET** , sélectionnez **MVC**, puis cliquez sur **OK**.

    ![Sélectionnez modèle MVC][VS2013SelectMVCTemplate]

1. Si vous n’êtes pas encore connecté à Microsoft Azure, vous devrez se connecter. Suivez les invites pour vous connecter à Azure.
2. Une fois que vous êtes connecté, vous pouvez commencer à configurer votre application web application Service. Spécifiez le **nom de l’application Web**, **plan de services d’application**, **groupe de ressources**et **région**, puis cliquez sur **créer**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Une fois le projet création terminé, attendez que l’application web soit créé dans le Service d’application Azure comme indiqué dans la fenêtre de **l’Activité de Service d’application Azure** . Ensuite, cliquez sur **Publier MyTaskListApp de cette application Web maintenant**.

1. Cliquez sur **Publier**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

    Une fois que votre application ASP.NET par défaut est publiée sur Azure Application Service Web Apps, il sera lancé dans le navigateur.

## <a name="install-the-mongodb-c-driver"></a>Installer le pilote MongoDB c#

MongoDB prend en charge côté client pour les applications c# via un pilote, vous devez installer sur votre ordinateur de développement local. Le pilote c# est disponible via NuGet.

Pour installer le pilote MongoDB c# :

1. Dans l' **Explorateur de solutions**, droit sur le projet **MyTaskListApp** et sélectionnez **Gérer NuGetPackages**.

    ![Gérer les Packages NuGet][VS2013ManageNuGetPackages]

2. Dans la fenêtre **Gérer les Packages NuGet** , dans le volet gauche, cliquez sur **en ligne**. Dans la zone de **Recherche en ligne** sur la droite, tapez « mongodb.driver ».  Cliquez sur **installer** pour installer le pilote.

    ![Rechercher MongoDB c# pilote][SearchforMongoDBCSharpDriver]

3. Cliquez sur **accepter** pour accepter la 10gen, les termes du contrat de licence Inc..

4. Une fois que le pilote a installé, cliquez sur **Fermer** .
    ![MongoDB c# pilote installé][MongoDBCsharpDriverInstalled]


Le pilote MongoDB c# est maintenant installé.  Références aux bibliothèques **MongoDB.Bson**, **MongoDB.Driver**et **MongoDB.Driver.Core** ont été ajoutés au projet.

![Références MongoDB c# pilote][MongoDBCSharpDriverReferences]

## <a name="add-a-model"></a>Ajout d’un modèle ##
Dans l' **Explorateur de solutions**, cliquez sur le dossier *modèles* et **Ajouter** une nouvelle **classe** et nommez-la *TaskModel.cs*.  Dans *TaskModel.cs*, remplacez le code existant par le code suivant :

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;
    
    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }
    
            [BsonElement("Name")]
            public string Name { get; set; }
    
            [BsonElement("Category")]
            public string Category { get; set; }
    
            [BsonElement("Date")]
            public DateTime Date { get; set; }
    
            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }
    
        }
    }

## <a name="add-the-data-access-layer"></a>Ajouter la couche d’accès aux données ##
Dans l' **Explorateur de solutions**, cliquez sur le projet *MyTaskListApp* et **Ajouter** un **Nouveau dossier** nommé *DAL*.  Clic droit sur le dossier *DAL* et **Ajouter** une nouvelle **classe**. Nommez le fichier de classe *Dal.cs*.  Dans *Dal.cs*, remplacez le code existant par le code suivant :

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    
    
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;
    
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
    
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
    
            // Default constructor.        
            public Dal()
            {
            }
    
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
    
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
    
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            # region IDisposable
    
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        if (mongoServer != null)
                        {
                            this.mongoServer.Disconnect();
                        }
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
        }
    }

## <a name="add-a-controller"></a>Ajouter un contrôleur ##
Ouvrez le fichier *Controllers\HomeController.cs* dans **L’Explorateur** et remplacez le code existant avec les éléments suivants :

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;
    
    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/
    
            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }
    
            //
            // GET: /MyTask/Create
    
            public ActionResult Create()
            {
                return View();
            }
    
            //
            // POST: /MyTask/Create
    
            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
                    return RedirectToAction("Index");
                }
                catch
                {
                    return View();
                }
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            # region IDisposable
    
            new protected void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            new protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        this.dal.Dispose();
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
    
        }
    }

## <a name="set-up-the-styles"></a>Configurer les styles ##
Pour modifier le titre en haut de la page, ouvrez la *Views\Shared\\_Layout.cshtml* dans **L’Explorateur** de fichiers et remplacez « Nom de l’Application » dans l’en-tête de la barre de navigation avec « Mes tâches liste Application » afin qu’il ressemble à ceci :

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Pour configurer le menu liste des tâches, ouvrez le fichier *\Views\Home\Index.cshtml* et remplacez le code existant par le code suivant :
    
    @model IEnumerable<MyTaskListApp.Models.MyTask>
    
    @{
        ViewBag.Title = "My Task List";
    }
    
    <h2>My Task List</h2>
    
    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
            
        </tr>
    
    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
            
        </tr>
    }
    
    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Pour ajouter la possibilité de créer une tâche, cliquez sur le *Views\Home\\ * dossier et **Ajouter** une **vue**.  Nommez le mode *Création*. Remplacez le code avec les éléments suivants :

    @model MyTaskListApp.Models.MyTask
    
    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
    
    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>
    
            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

**Explorateur de solutions** doit ressembler à ceci :

![Explorateur de solutions][SolutionExplorerMyTaskListApp]

## <a name="set-the-mongodb-connection-string"></a>Définir la chaîne de connexion MongoDB ##
Dans l' **Explorateur de solutions**, ouvrez le fichier *DAL/Dal.cs* . Recherchez la ligne suivante de code :

    private string connectionString = "mongodb://<vm-dns-name>";

Remplacer `<vm-dns-name>` avec le nom DNS de la machine virtuelle en cours d’exécution MongoDB que vous avez créé à l’étape [créer une machine virtuelle et installer MongoDB][] de ce didacticiel.  Pour trouver le nom DNS de votre machine virtuelle, accédez au portail Azure, sélectionnez **Machines virtuelles**et trouver **Le nom de DNS**.

Si le nom DNS de la machine virtuelle est « testlinuxvm.cloudapp.net » et MongoDB est à l’écoute sur le port par défaut 27017, la ligne de chaîne de connexion de code ressemblera :

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Si le point de terminaison machine virtuelle spécifie un autre port externes pour MongoDB, vous pouvez spécifier le port dans la chaîne de connexion :

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Pour plus d’informations sur les chaînes de connexion MongoDB, voir [connexions][MongoConnectionStrings].

## <a name="test-the-local-deployment"></a>Tester le déploiement local ##

Pour exécuter votre application sur votre ordinateur de développement, sélectionnez **Démarrer le débogage** dans le menu **Déboguer** ou appuyez sur **F5**. IIS Express démarre et un navigateur s’ouvre et ouvre la page d’accueil de l’application.  Vous pouvez ajouter une nouvelle tâche, est ajoutée à la base de données MongoDB s’exécutant sur votre ordinateur virtuel dans Azure.

![Mon Application de liste de tâches][TaskListAppBlank]

## <a name="publish-to-azure-app-service-web-apps"></a>Publier à Azure Application Service Web Apps

Dans cette section, vous allez publier vos modifications à Azure Application Service Web Apps.

1. Dans l’Explorateur, **MyTaskListApp** avec le bouton droit à nouveau, puis cliquez sur **Publier**.
2. Cliquez sur **Publier**.

    Vous devez maintenant voir votre application web en cours d’exécution dans le Service d’application Azure et accéder à la base de données MongoDB dans le Machines virtuelles Azure.

## <a name="summary"></a>Résumé ##

Vous avez déployé avec succès votre application ASP.NET à Azure Application Service Web Apps. Pour afficher l’application web :

1. Connectez-vous au portail Azure.
2. Cliquez sur **applications Web**. 
3. Sélectionnez votre application web dans la liste **d’Applications Web** .

Pour plus d’informations sur le développement d’applications c# contre MongoDB, voir le [Centre de langue CSharp][MongoC#LangCenter]. 

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-windows-classic-install-mongodb.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Créer une machine virtuelle et installer MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 