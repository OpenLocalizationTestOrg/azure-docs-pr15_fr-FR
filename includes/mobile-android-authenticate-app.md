
1. Dans l' **Explorateur de projets** dans Studio Android, ouvrez le fichier ToDoActivity.java et ajoutez les instructions d’importation suivantes.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Ajoutez la méthode suivante à la classe **ToDoActivity** : 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    Cela crée une nouvelle méthode pour gérer le processus d’authentification. L’utilisateur est authentifié à l’aide d’une connexion de Google. Une boîte de dialogue s’affiche et affiche l’ID de l’utilisateur authentifié. Vous ne pouvez pas continuer sans une authentification positive.

    > [AZURE.NOTE] Si vous utilisez un fournisseur d’identité autre que Google, modifiez la valeur passée à la méthode de **connexion** au-dessus d’une des opérations suivantes : _MicrosoftAccount_, _Facebook_, _Twitter_ou _windowsazureactivedirectory_.

3. Dans la méthode **onCreate** , ajoutez la ligne suivante de code après le code instancie la `MobileServiceClient` objet.

        authenticate();

    Cet appel démarre le processus d’authentification.

4. Déplacer le code restant après `authenticate();` dans la méthode **onCreate** pour une nouvelle méthode **Create table** , qui ressemble à ceci :

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. Dans le menu **exécution** , puis cliquez sur **exécuter l’application** pour démarrer l’application et connectez-vous à l’aide de votre fournisseur d’identité choisie. 

    Lorsque vous êtes connecté dans avec succès, l’application doit s’exécuter sans erreurs et vous devriez pouvoir interroger le service de serveur principal et mettre à jour de données.