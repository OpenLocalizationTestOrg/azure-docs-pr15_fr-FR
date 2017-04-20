###<a name="server-auth"></a>Comment : vous authentifier avec un fournisseur (flux Server)

Pour que les applications Mobile gérer le processus d’authentification dans votre application, vous devez enregistrer votre application auprès de votre fournisseur d’identité. Dans votre Service d’application Azure, vous devez configurer les ID de l’application et secret fourni par votre fournisseur.
Pour plus d’informations, consultez le didacticiel [authentification ajouter à votre application].

Une fois que vous avez enregistré votre fournisseur d’identité, appelez simplement la méthode .login() avec le nom de votre fournisseur. Par exemple, pour vous connecter avec Facebook utiliser le code suivant.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Si vous utilisez un fournisseur d’identité autre que Facebook, modifiez la valeur passée à la méthode de connexion au-dessus d’une des opérations suivantes : `microsoftaccount`, `facebook`, `twitter`, `google`, ou `aad`.

Dans ce cas, Azure Application Service gère le flux d’authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et générer un jeton d’authentification de Service d’application après la connexion établie avec le fournisseur d’identité. La fonction de connexion, lorsque vous avez terminé, renvoie un objet JSON (utilisateur) qui expose les ID d’utilisateur et le Service d’application jeton d’authentification dans les champs nom d’utilisateur et authenticationToken, respectivement. Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu’il arrive à expiration.

###<a name="client-auth"></a>Comment : vous authentifier avec un fournisseur (flux Client)

Votre application peut également indépendamment contactez le fournisseur d’identité et puis fournir le jeton retourné à votre Service d’application pour l’authentification. Ce flux client vous permet de fournir une expérience de connexion unique pour les utilisateurs ou pour extraire des données utilisateur supplémentaires à partir du fournisseur d’identité.

#### <a name="social-authentication-basic-example"></a>Exemple de base de l’authentification mise en réseau

Cet exemple utilise Facebook client SDK pour l’authentification :

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Cet exemple suppose que le jeton fourni par le fournisseur respectif SDK est stocké dans la variable jeton.

#### <a name="microsoft-account-example"></a>Exemple de Account Microsoft

L’exemple suivant utilise le Kit de développement Live prend en charge unique-authentification pour les applications du Windows Store à l’aide de Microsoft Account :

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

Cet exemple obtient un jeton de Live Connect, qui est fourni par appel de la fonction de connexion à votre Service d’application.

###<a name="auth-getinfo"></a>Comment : obtenir des informations sur l’utilisateur authentifié

Les informations d’authentification de l’utilisateur actuel peuvent être récupérées à partir de la `/.auth/me` point de terminaison à l’aide de n’importe quelle méthode AJAX.  Veillez à définir la `X-ZUMO-AUTH` en-tête à votre jeton d’authentification.  Le jeton d’authentification est stocké dans `client.currentUser.mobileServiceAuthenticationToken`.  Par exemple, pour utiliser l’extraction API :

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Extraction est disponible sous forme d’un package de npm ou à partir de CDNJS de navigateur. Vous pouvez également utiliser jQuery ou une autre API AJAX pour récupérer les informations.  Les données seront reçues en tant qu’objet JSON.
