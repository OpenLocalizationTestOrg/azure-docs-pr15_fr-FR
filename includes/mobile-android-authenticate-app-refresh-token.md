Notre du cache de jetons doit travailler dans un cas simple mais, que se passe-t-il lorsque le jeton arrive à expiration ou est révoqué ? Le jeton peut arriver à expiration lors de l’application n’est pas en cours d’exécution. Cela signifie que le cache de jetons n’est pas valide. Le jeton pourrait également expirer alors que l’application est en cours d’exécution. Le résultat est un état HTTP code 401 « non autorisé ». 

Nous avons besoin d’être en mesure de détecter un jeton qui a expiré et l’actualiser. Pour ce faire, nous utilisons une [ServiceFilter](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html) à partir de la [bibliothèque cliente Android](http://dl.windowsazure.com/androiddocs/).

Dans cette section, vous définirez un ServiceFilter qui détecte une réponse 401 du code d’état HTTP et déclencher l’actualisation du jeton et le cache de jetons. Par ailleurs, cette ServiceFilter bloque les autres sortants demandes lors de l’authentification afin que ces demandes utilisent le jeton actualisé.

1. Ouvrez le fichier ToDoActivity.java et ajoutez les instructions d’importation suivantes :
 
        import java.util.concurrent.atomic.AtomicBoolean;
        import java.util.concurrent.ExecutionException;

        import com.microsoft.windowsazure.mobileservices.MobileServiceException;
 
2. Ajoutez les membres suivants à le `ToDoActivity` cours. 

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    Ils seront utilisées afin de synchroniser l’authentification de l’utilisateur. Nous voulons uniquement s’authentifier une seule fois. Tous les appels lors de l’authentification doivent attendre et utilisez le nouveau jeton à partir de l’authentification en cours.

3. Dans le fichier ToDoActivity.java, ajoutez la méthode suivante à la classe ToDoActivity qui sera utilisée pour bloquer les appels sortants sur d’autres threads alors que l’authentification est en cours.

        /**
         * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
         */
        public boolean detectAndWaitForAuthentication()
        {
            boolean detected = false;
            synchronized(mAuthenticationLock)
            {
                do
                {
                    if (bAuthenticating == true)
                        detected = true;
                    try
                    {
                        mAuthenticationLock.wait(1000);
                    }
                    catch(InterruptedException e)
                    {}
                }
                while(bAuthenticating == true);
            }
            if (bAuthenticating == true)
                return true;
            
            return detected;
        }
        

4. Dans le fichier ToDoActivity.java, ajoutez la méthode suivante à la classe ToDoActivity. Cette méthode déclenche l’attente, puis mettez à jour le jeton de requêtes sortantes lorsque l’authentification est terminée. 

        
        /**
         * Waits for authentication to complete then adds or updates the token 
         * in the X-ZUMO-AUTH request header.
         * 
         * @param request
         *            The request that receives the updated token.
         */
        private void waitAndUpdateRequestToken(ServiceFilterRequest request)
        {
            MobileServiceUser user = null;
            if (detectAndWaitForAuthentication())
            {
                user = mClient.getCurrentUser();
                if (user != null)
                {
                    request.removeHeader("X-ZUMO-AUTH");
                    request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
                }
            }
        }


5. Dans le fichier ToDoActivity.java, mettez à jour la `authenticate` afin qu’il accepte un paramètre booléen pour permettre à forcer l’actualisation du cache jeton et jeton de la méthode de la ToDoActivity classe. Nous devons également signaler les threads bloqués quand l’authentification terminée afin qu’ils peuvent Décrochez le nouveau jeton.

        /**
         * Authenticates with the desired login provider. Also caches the token. 
         * 
         * If a local token cache is detected, the token cache is used instead of an actual 
         * login unless bRefresh is set to true forcing a refresh.
         * 
         * @param bRefreshCache
         *            Indicates whether to force a token refresh. 
         */
        private void authenticate(boolean bRefreshCache) {
            
            bAuthenticating = true;
            
            if (bRefreshCache || !loadUserTokenCache(mClient))
            {
                // New login using the provider and update the token cache.
                mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                        new UserAuthenticationCallback() {
                            @Override
                            public void onCompleted(MobileServiceUser user,
                                    Exception exception, ServiceFilterResponse response) {
    
                                synchronized(mAuthenticationLock)
                                {
                                    if (exception == null) {
                                        cacheUserToken(mClient.getCurrentUser());
                                        createTable();
                                    } else {
                                        createAndShowDialog(exception.getMessage(), "Login Error");
                                    }
                                    bAuthenticating = false;
                                    mAuthenticationLock.notifyAll();
                                }
                            }
                        });
            }
            else
            {
                // Other threads may be blocked waiting to be notified when 
                // authentication is complete.
                synchronized(mAuthenticationLock)
                {
                    bAuthenticating = false;
                    mAuthenticationLock.notifyAll();
                }
                createTable();
            }
        }   



6. Dans le fichier ToDoActivity.java, ajouter ce code pour une nouvelle `RefreshTokenCacheFilter` classe à l’intérieur de la classe ToDoActivity :

        /**
        * The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
         * When 401 is encountered, the filter calls the authenticate method on the 
         * UI thread. Out going requests and retries are blocked during authentication. 
         * Once authentication is complete, the token cache is updated and 
         * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
         * that request.   
         */
        private class RefreshTokenCacheFilter implements ServiceFilter {
         
            AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();                     
            
            @Override
            public ListenableFuture<ServiceFilterResponse> handleRequest(
                    final ServiceFilterRequest request, 
                    final NextServiceFilterCallback nextServiceFilterCallback
                    )
            {
                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);
     
                // Send the request down the filter chain
                // retrying up to 5 times on 401 response codes.
                ListenableFuture<ServiceFilterResponse> future = null;
                ServiceFilterResponse response = null;
                int responseCode = 401;
                for (int i = 0; (i < 5 ) && (responseCode == 401); i++)
                {
                    future = nextServiceFilterCallback.onNext(request);
                    try {
                        response = future.get();
                        responseCode = response.getStatus().getStatusCode();
                    } catch (InterruptedException e) {
                       e.printStackTrace();
                    } catch (ExecutionException e) {
                        if (e.getCause().getClass() == MobileServiceException.class)
                        {
                            MobileServiceException mEx = (MobileServiceException) e.getCause();
                            responseCode = mEx.getResponse().getStatus().getStatusCode();
                            if (responseCode == 401)
                            {
                                // Two simultaneous requests from independent threads could get HTTP status 401. 
                                // Protecting against that right here so multiple authentication requests are
                                // not setup to run on the UI thread.
                                // We only want to authenticate once. Requests should just wait and retry 
                                // with the new token.
                                if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                                                                                                      
                                {
                                    // Authenticate on UI thread
                                    runOnUiThread(new Runnable() {
                                        @Override
                                        public void run() {
                                            // Force a token refresh during authentication.
                                            authenticate(true);
                                        }
                                    });
                                }
    
                                // Wait for authentication to complete then update the token in the request. 
                                waitAndUpdateRequestToken(request);
                                mAtomicAuthenticatingFlag.set(false);                                                  
                            }
                        }
                    }
                }
                return future;
            }
        }


    Ce filtre de service doit vérifier chaque réponse pour état HTTP code 401 « non autorisé ». Si vous rencontrez un 401, une nouvelle demande de connexion pour obtenir un nouveau jeton sera le programme d’installation sur le thread de l’interface utilisateur. D’autres appels seront bloquées jusqu'à ce que la connexion est terminée, ou 5 tentatives. Si le nouveau jeton est obtenu, la requête qui a déclenché le 401 sera retentée avec le nouveau jeton et tous les appels bloqués seront retentés avec le nouveau jeton. 

7. Dans le fichier ToDoActivity.java, ajouter ce code pour une nouvelle `ProgressFilter` classe à l’intérieur de la classe ToDoActivity :
        
        /**
        * The ProgressFilter class renders a progress bar on the screen during the time the App is waiting for the response of a previous request.
        * the filter shows the progress bar on the beginning of the request, and hides it when the response arrived.
        */
        private class ProgressFilter implements ServiceFilter {
            @Override
            public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback nextServiceFilterCallback) {

                final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();

                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
                    }
                });

                ListenableFuture<ServiceFilterResponse> future = nextServiceFilterCallback.onNext(request);

                Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
                    @Override
                    public void onFailure(Throwable e) {
                        resultFuture.setException(e);
                    }

                    @Override
                    public void onSuccess(ServiceFilterResponse response) {
                        runOnUiThread(new Runnable() {

                            @Override
                            public void run() {
                                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.GONE);
                            }
                        });

                        resultFuture.set(response);
                    }
                });

                return resultFuture;
            }
        }
        
    Ce filtre affiche la barre de progression sur le début de la demande et pour masquer lorsque la réponse est arrivé.

8. Dans le fichier ToDoActivity.java, mettez à jour la `onCreate` méthode comme suit :

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            
            setContentView(R.layout.activity_to_do);
            mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);
        
            // Initialize the progress bar
            mProgressBar.setVisibility(ProgressBar.GONE);
        
            try {
                // Create the Mobile Service Client instance, using the provided
                // Mobile Service URL and key
                mClient = new MobileServiceClient(
                        "https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
                        "<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());
            
                // Authenticate passing false to load the current token cache if available.
                authenticate(false);
                    
            } catch (MalformedURLException e) {
                createAndShowDialog(new Exception("Error creating the Mobile Service. " +
                    "Verify the URL"), "Error");
            }
        }


       In this code, `RefreshTokenCacheFilter` is used in addition to `ProgressFilter`. Also during `onCreate` we want to load the token cache. So `false` is passed in to the `authenticate` method.


