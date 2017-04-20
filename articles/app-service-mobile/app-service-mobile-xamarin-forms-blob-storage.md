<properties
    pageTitle="Se connecter au stockage Azure dans votre application Xamarin.Forms"
    description="Ajouter des images à l’application mobile todo liste Xamarin.Forms en vous connectant à Azure blob storage"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Se connecter au stockage Azure dans votre application Xamarin.Forms

## <a name="overview"></a>Vue d’ensemble

Applications Mobile Azure client et serveur SDK prend en charge synchronisation hors connexion de données structurées avec opérations CRUD sur le point de terminaison /tables. En règle générale, ces données sont stockées dans une base de données ou d’un magasin similaire et généralement ces banques de données ne peut pas stocker les données binaires volumineuses efficacement. De même, certaines applications ont associé données sont stockée ailleurs (par exemple, stockage d’objets blob, partages de fichiers), et il est utile de pouvoir créer des associations entre des enregistrements dans le point de terminaison /tables et autres données.

Cette rubrique vous montre comment ajouter prise en charge des images à la liste de todo applications Mobile démarrage rapide. Vous devez effectuer tout d’abord le didacticiel [créer une application Xamarin.Forms].

Dans ce didacticiel, vous créez un compte de stockage et ajouter une chaîne de connexion à votre serveur principal application Mobile. Vous ajouterez ensuite un nouveau héritant du nouveau type d’applications Mobile `StorageController<T>` à votre projet de serveur.

>[AZURE.TIP] Ce didacticiel inclut un [exemple associé](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) disponible, qui peut être déployé à votre compte Azure. 

## <a name="prerequisites"></a>Conditions préalables

* Utiliser le didacticiel [créer une application Xamarin.Forms] , qui indique les autres conditions préalables. Cet article utilise l’application achevée à partir de ce didacticiel.

>[AZURE.NOTE] Si vous voulez commencer avec le Service d’application Azure avant de vous inscrivez à un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile). Vous pouvez créer une application mobile starter courte immédiatement dans le Service d’application : aucune carte de crédit obligatoire et aucun engagements.

## <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Créer un compte de stockage en suivant ce didacticiel, [créez un compte de stockage Azure]. 

2. Dans le portail Azure, accédez à votre compte de stockage nouvellement créé, puis cliquez sur l’icône de **touches** . Copiez la **chaîne de connexion principal**.

3. Accédez à votre version de serveur de l’application mobile. Sous **Paramètres de tous les** -> **Paramètres de l’Application** -> **Chaînes de connexion**, créer une nouvelle clé nommée `MS_AzureStorageAccountConnectionString` et utilisez la valeur copiée à partir de votre compte de stockage. Utiliser l’option **Personnaliser** en tant que le type de clé.

## <a name="add-a-storage-controller-to-the-server"></a>Ajouter un contrôleur de stockage sur le serveur

Vous devez ajouter un nouveau contrôleur à votre projet serveur qui sera répondre à des demandes pour un jeton associations de sécurité pour le stockage Azure, ainsi que retourner une liste de fichiers qui correspondent à un enregistrement :

- [Ajouter un contrôleur de stockage à votre projet serveur](#add-controller-code)
- [Itinéraires enregistrés par le contrôleur de stockage](#routes-registered)
- [Communication client et serveur](#client-communication)

###<a name="add-controller-code"></a>Ajouter un contrôleur de stockage à votre projet serveur

1. Dans Visual Studio, ouvrez votre projet server .NET. Ajoutez le package Nuget [Microsoft.Azure.Mobile.Server.Files]. Veillez à sélectionner **inclure la version préliminaire**.

2. Dans Visual Studio, ouvrez votre projet server .NET. Cliquez sur le dossier **contrôleurs** , puis sélectionnez **Ajouter** -> **contrôleur** -> **Contrôleur de 2 de l’API Web - vide**. Nommez le contrôleur `TodoItemStorageController`.

3. Ajoutez les éléments suivants à l’aide des instructions :

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Modifier la classe de base `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Ajoutez les méthodes suivantes à la classe :

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Mettre à jour la configuration de l’API Web pour définir le routage de l’attribut. Dans **Startup.MobileApp.cs**, ajoutez la ligne suivante à la `ConfigureMobileApp()` méthode, après la définition de la `config` variable :

        config.MapHttpAttributeRoutes();

7. Publier votre projet server à votre version de serveur de l’application mobile.

###<a name="routes-registered"></a>Itinéraires enregistrés par le contrôleur de stockage

La nouvelle `TodoItemStorageController` expose deux ressources sous-adresse sous l’enregistrement il gère :

- StorageToken

    + HTTP POST : Crée un jeton de stockage
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET : Récupère une liste de fichiers associé à l’enregistrement
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + SUPPRESSION de HTTP : Supprime le fichier spécifié dans l’identificateur de ressource de fichier
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>Communication client et serveur

Notez que `TodoItemStorageController` signifie *pas* dispose d’un itinéraire de chargement ou le téléchargement d’un blob. C’est parce qu’un client mobile interagit avec les objets blob storage *directement* pour effectuer ces opérations, après la première prise un jeton associations de sécurité (Signature accès partagés) un accès sécurisé à un blob particulier ou un conteneur. Il s’agit d’une conception architecturale importantes, comme dans le cas contraire accès au stockage seraient limité par la disponibilité de la version serveur mobile et extensibilité élevées. À la place, en vous connectant directement au stockage Azure, le client mobile peut tirer parti de ses fonctionnalités telles que partition automatique et geo Student.

Une signature accès partagé offre un accès délégué aux ressources dans votre compte de stockage. Cela signifie que vous pouvez accorder qu'un client des autorisations limitées aux objets de votre compte de stockage pour une période spécifiée de temps et avec un ensemble spécifique d’autorisations, sans avoir à partager les touches d’accès de votre compte. Pour plus d’informations, voir [Fonctionnement partagés accès Signatures].

L’illustration ci-dessous présente les interactions client et serveur. Avant de télécharger un fichier, le client demande un jeton associations de sécurité à partir du service. Le service utilise la chaîne de connexion de stockage pour générer une nouvelles associations de sécurité, elle renvoie ensuite au client. Les associations de sécurité sont limitée dans le temps et limite les autorisations à simplement un fichier particulier ou un conteneur. Le client mobile utilise ensuite cette associations de sécurité et le client de stockage Azure SDK à télécharger le fichier vers le stockage blob.

![Demande un jeton associations de sécurité](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>Mettre à jour votre application client pour ajouter la prise en charge de l’image

Ouvrez le projet de démarrage rapide Xamarin.Forms dans Visual Studio ou Xamarin Studio. Vous installerez packages Nuget et mettre à jour le projet de bibliothèque portable et les iOS, Android et Windows projets clients :

- [Ajouter des packages Nuget](#add-nuget)
- [Ajouter une interface IPlatform](#add-iplatform)
- [Ajouter une classe FileHelper](#add-filehelper)
- [Ajouter un gestionnaire de synchronisation de fichier](#file-sync-handler)
- [Mise à jour TodoItemManager](#update-todoitemmanager)
- [Ajouter un mode Détails](#add-details-view)
- [Mise à jour de l’affichage principale](#update-main-view)
- [Mettre à jour le projet Android](#update-android), [project iOS](#update-ios), [projet Windows](#update-windows)

>[AZURE.NOTE] Ce didacticiel contient uniquement des instructions pour le Windows Store plates-formes, pas Windows Phone, iOS et Android.

###<a name="add-nuget"></a>Ajouter des packages Nuget

Avec le bouton droit de la solution et sélectionnez **packages Nuget gérer pour la solution**. Ajoutez les packages Nuget suivants à **tous les** projets dans la solution. Veillez à **inclure la version préliminaire**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Pour faciliter la tâche, cet exemple utilise la bibliothèque [PCLStorage] , mais elle n’est pas requise par le client Azure Mobile applications SDK.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>Ajouter une interface IPlatform

Créer une nouvelle interface `IPlatform` dans le projet principal bibliothèque portable. Cela suit le modèle [Xamarin.Forms DependencyService] pour charger la classe droite spécifique à la plateforme en cours d’exécution. Vous ajouterez ultérieurement spécifique à la plateforme mises en œuvre dans chacun des projets client.

1. Ajoutez les éléments suivants à l’aide des instructions :

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Remplacez l’implémentation avec les éléments suivants :

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>Ajouter une classe FileHelper

1. Créer une nouvelle classe `FileHelper` dans le projet principal bibliothèque portable. Ajoutez les éléments suivants à l’aide des instructions :

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Ajouter la définition de classe :

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>Ajouter un gestionnaire de synchronisation de fichier

Créer une nouvelle classe `TodoItemFileSyncHandler` dans le projet principal bibliothèque portable. Cette classe contient des rappels à partir du SDK Azure pour signaler votre code quand un fichier est ajouté ou supprimé.

Le Kit de développement du Client Mobile Azure ne stocke pas réellement des données de fichier : le Kit de développement logiciel client appelle votre implémentation de `IFileSyncHandler` qui à son tour détermine si et comment les fichiers stockés sur le périphérique local.

1. Ajoutez les éléments suivants à l’aide des instructions :

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Remplacez la définition de classe avec les éléments suivants : 

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>Mise à jour TodoItemManager

1. Dans **TodoItemManager.cs**, ne commentez pas la ligne `#define OFFLINE_SYNC_ENABLED`.

2. Dans **TodoItemManager.cs**, ajoutez les éléments suivants à l’aide des instructions :

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. Dans le constructeur de `TodoItemManager`, ajoutez le code suivant après l’appel à `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. Dans le constructeur, remplacez l’appel à `InitializeAsync` avec les éléments suivants. Ainsi, qu’il n’y a rappels lorsque des enregistrements sont modifiées dans le magasin local. La fonctionnalité de synchronisation fichier utilise ces rappels pour déclencher votre gestionnaire de synchronisation de fichier.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. Dans `SyncAsync()`, ajoutez le code suivant après l’appel à `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Ajoutez les méthodes suivantes pour `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>Ajouter un mode Détails

Dans cette section, vous allez ajouter une nouvelle vue Détails pour un élément todo. La vue est créée lorsque l’utilisateur sélectionne un élément todo et vous permet de nouvelles images à ajouter à un élément.

1. Ajoutez une nouvelle classe **TodoItemImage** au projet bibliothèque portable avec l’implémentation suivante :

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Modifier **App.cs**. Remplacer l’initialisation de `MainPage` avec les éléments suivants :
    
        MainPage = new NavigationPage(new TodoList());

3. Dans **App.cs**, ajoutez la propriété suivante :

        public static object UIContext { get; set; }

4. Droit sur le projet bibliothèque portable, puis sélectionnez **Ajouter** -> **Un nouvel élément** -> **disponibilité sur plusieurs plateformes** -> **Xaml Page de formulaires**. Nommez la vue `TodoItemDetailsView`.

5. Ouvrez **TodoItemDetailsView.xaml** et remplacez le corps de la contenuPage avec les éléments suivants :

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Modifier **TodoItemDetailsView.xaml.cs** et ajoutez les éléments suivants à l’aide des instructions :

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Remplacer l’implémentation de `TodoItemDetailsView` avec les éléments suivants :

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>Mise à jour de l’affichage principale 

Mettre à jour de la vue principale pour ouvrir l’affichage Détails lorsqu’un élément todo est sélectionné.

Dans **TodoList.xaml.cs**, remplacez l’implémentation de `OnSelected` avec les éléments suivants :

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>Mettre à jour le projet Android

Ajouter un code plateforme spécifique au projet Android, y compris le code pour le téléchargement d’un fichier et l’utilisation de la caméra pour capturer une nouvelle image. 

Ce code utilise le Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) pour charger la classe droite spécifique à la plateforme en cours d’exécution.

1. Ajouter le composant **Xamarin.Mobile** au projet Android.

2. Ajoutez une nouvelle classe `DroidPlatform` avec l’implémentation suivante. Remplacez « Votre_espace_de_noms » par l’espace de noms principal de votre projet.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modifier **MainActivity.cs**. Dans `OnCreate`, ajoutez le code suivant avant l’appel à `LoadApplication()`:

        App.UIContext = this;

###<a name="update-ios"></a>Mettre à jour le projet iOS

Ajouter un code plateforme spécifique au projet iOS.

1. Ajouter le composant **Xamarin.Mobile** au projet iOS.

2. Ajoutez une nouvelle classe `TouchPlatform` avec l’implémentation suivante. Remplacez « Votre_espace_de_noms » par l’espace de noms principal de votre projet.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modifier **AppDelegate.cs** et supprimez les commentaires de l’appel vers `SQLitePCL.CurrentPlatform.Init()`.

###<a name="update-windows"></a>Mettre à jour le projet de Windows

1. Installez l’extension Visual Studio [SQLite pour Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Pour plus d’informations, consultez le didacticiel [Activer la synchronisation hors connexion pour votre application Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Modifier **Package.appxmanifest** et vérifier les capacités de la **Webcam** .

3. Ajoutez une nouvelle classe `WindowsStorePlatform` avec l’implémentation suivante. Remplacez « Votre_espace_de_noms » par l’espace de noms principal de votre projet.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##<a name="summary"></a>Résumé

Cet article décrit comment utiliser la nouvelle prise en charge du fichier dans le client Mobile Azure et le Kit de développement logiciel avec stockage Azure. 

- Créer un compte de stockage et ajoutez la chaîne de connexion à votre serveur principal de l’application mobile. Seul le serveur principal dispose de la clé au stockage Azure : le client mobile demande un jeton associations de sécurité (partagés accès Signature) chaque fois qu’il a besoin accéder au stockage Azure. Pour plus d’informations sur les jetons associations de sécurité dans le stockage Azure, voir [Présentation partagés accès Signatures].

- Créer un contrôleur qui sous-classe `StorageController` afin de gérer les demandes de jeton associations de sécurité et pour obtenir les fichiers qui sont associés à un enregistrement. Par défaut, les fichiers sont associés à un enregistrement à l’aide de l’ID d’enregistrement dans le cadre du nom du conteneur ; le comportement peut être personnalisé en spécifiant une implémentation de `IContainerNameResolver`. La stratégie jeton sa peut également être personnalisée.

- Le Kit de développement du Client Mobile Azure n’enregistre pas réellement stocker des données de fichier. Au lieu de cela, le client SDK appelle votre `IFileSyncHandler`, qui ensuite décide comment (et si) fichiers stockés sur le périphérique local. Le Gestionnaire de synchronisation est enregistré comme suit :

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource`est appelé lorsque le Kit de développement du Client Mobile Azure doit les données du fichier (par exemple, dans le cadre du processus de téléchargement). Cela vous donne la possibilité de gérer comment (et si) fichiers sont stockées sur le périphérique local et retourner cette information lorsque cela est nécessaire.

      + `IFileSyncHandler.ProcessFileSynchronizationAction`est appelé dans le cadre du flux de synchronisation de fichier. Une référence de fichier et une valeur d’énumération FileSynchronizationAction sont fournis afin que vous puissiez décider comment votre application doit gérer cet événement (par exemple, télécharger automatiquement un fichier lorsqu’il est créé ou mis à jour, suppression d’un fichier à partir de l’appareil local lorsque ce fichier est supprimé sur le serveur).

- A `MobileServiceFile` peut être utilisé en mode en ligne ou hors connexion, en utilisant un `IMobileServiceTable` ou `IMobileServiceSyncTable`, respectivement. Dans le scénario en mode hors connexion, le téléchargement se produit lorsque l’application appelle `PushFileChangesAsync`. Dans ce cas, la file d’attente opération en mode hors connexion soient traités ; pour chaque opération de fichier, le client Mobile Azure SDK appelle la `GetDataSource` méthode sur le `IFileSyncHandler` instance pour extraire le contenu du fichier pour le téléchargement.

- Afin de récupérer des fichiers d’un élément, appelez le '' GetFilesAsync` method on the  `IMobileServiceTable<T> ` or IMobileServiceSyncTable<T>` instance. Cette méthode renvoie une liste des fichiers associés à l’élément de données. (Remarque : il s’agit d’une opération *locale* et renverra les fichiers basées sur l’état de l’objet lors de la dernière synchronisation. Pour obtenir une liste à jour des fichiers à partir du serveur, vous devez lancer une opération de synchronisation tout d’abord.)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- La fonctionnalité de synchronisation de fichier utilise des notifications de modification de l’enregistrement dans le magasin local pour récupérer les enregistrements qui le client reçu dans le cadre d’une opération d’envoi ou d’extraction. Pour cela, en activant les notifications locale et serveur pour le contexte de synchronisation à l’aide de la `StoreTrackingOptions` paramètre. 

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Autres options de suivi store sont disponibles, telles que les notifications local uniquement ou serveur uniquement. Vous pouvez ajouter ou propre rappel personnalisé à l’aide du `EventManager` propriété du `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Il est possible d’ajouter ou supprimer des fichiers à partir d’un enregistrement en modifiant le stockage blob directement, étant donné que l’association est réalisée via une convention d’appellation. Toutefois, dans ce cas vous devez toujours **mettre à jour l’horodatage d’enregistrement lorsque les objets BLOB associées sont modifiés**. Le client Mobile Azure SDK met toujours à jour un enregistrement lorsque vous ajoutez ou suppression d’un fichier. 

    La raison de cette exigence est que certains clients mobiles déjà aura l’enregistrement dans le stockage local. Lorsque ces clients effectuent une extraction incrémentielle, cet enregistrement n’est pas renvoyé et le client ne demande pas les nouveaux fichiers associés. Pour éviter ce problème, il est recommandé de mettre à jour l’enregistrement horodatage lorsque vous effectuez des modifications de stockage blob qui n’utilisent pas le client Mobile Azure SDK.

- Le projet client utilise le modèle [Xamarin.Forms DependencyService] pour charger la classe droite spécifique à la plateforme en cours d’exécution. Dans cet exemple, nous avons défini une interface `IPlatform` avec mises en œuvre dans chacun des projets spécifique à la plateforme.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Créer une application Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Présentation des Signatures accès partagés]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Créer un compte de stockage Azure]:  ../storage/storage-create-storage-account.md#create-a-storage-account
