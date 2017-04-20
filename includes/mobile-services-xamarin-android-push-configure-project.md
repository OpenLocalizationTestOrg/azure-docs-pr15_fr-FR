
1. Dans l’affichage (ou l' **Explorateur de solutions** dans Visual Studio) Solution, cliquez sur le dossier **composants** et cliquez sur **Obtenir plus composants...**, recherchez le composant de **Client de messagerie Google Cloud** ajoutez au projet.

2. Ouvrez le fichier de projet ToDoActivity.cs et ajoutez les éléments suivants à l’aide de l’instruction à la classe :

        using Gcm.Client;

3. Dans la classe **ToDoActivity** , ajoutez le nouveau code suivant : 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    Cela vous permet d’accéder à l’instance du client mobile depuis le processus de service Gestionnaire push.

4.  Ajoutez le code suivant à la méthode **OnCreate** , après avoir créé le **MobileServiceClient** :

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Votre **ToDoActivity** est maintenant prêt pour l’ajout de notifications de transmission.