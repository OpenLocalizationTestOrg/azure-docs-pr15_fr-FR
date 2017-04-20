
L’exemple précédent a montré une norme de connexion, qui exige que le client de contacter le fournisseur d’identité et le système principal service Azure chaque fois que l’application démarre. Non seulement cette méthode est inefficace, que vous pouvez rencontrer des problèmes liés à l’utilisation de nombreux clients conseillé de vous lancer l’application en même temps. Une meilleure approche consiste à mettre en cache le jeton d’autorisation renvoyé par le service Azure et essayez d’utiliser cette première avant d’utiliser une connexion basée sur le fournisseur. 

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par le service Azure indépendamment que vous utilisiez gérés par le client ou gérés par le service d’authentification serveur principal. Ce didacticiel utilise gérés par le service d’authentification.


1. Ouvrez le fichier ToDoActivity.java et ajoutez les instructions d’importation suivantes :

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Ajouter les membres suivants à la `ToDoActivity` cours.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. Dans le fichier ToDoActivity.java, ajoutez la la définition suivante pour la `cacheUserToken` méthode.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Cette méthode stocke l’id d’utilisateur et le jeton dans un fichier de préférences est marqué comme privé. Cela doit protéger l’accès au cache afin que d’autres applications sur l’appareil n’ont pas accès au jeton, car la préférence est en mode sandbox pour l’application. Toutefois, si quelqu'un a accès à l’appareil, il est possible qu’ils peuvent accéder au cache jeton par d’autres moyens. 

    >[AZURE.NOTE]Vous pouvez protéger davantage le jeton avec chiffrement si jeton accès à vos données sont considérées comme hautement sensibles et une personne peut accéder à l’appareil. Toutefois, une solution complètement sécurisée est au-delà de la portée de ce didacticiel et dépendantes vos besoins en matière de sécurité.


4. Dans le fichier ToDoActivity.java, ajoutez la la définition suivante pour la `loadUserTokenCache` méthode.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. Dans le fichier *ToDoActivity.java* , remplacez le `authenticate` méthode avec la méthode suivante qui utilise un cache de jetons. Remplacer le fournisseur de connexion si vous souhaitez utiliser un compte différent de Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Créer l’authentification de l’application et test à l’aide d’un compte valide. Exécutez au moins deux fois. Lors de la première exécution, vous devez recevoir une invite pour se connecter et créer le cache de jetons. Après cela, chaque exécution tentera de charger le cache de jetons pour l’authentification et ne soyez pas obligé de connexion.



