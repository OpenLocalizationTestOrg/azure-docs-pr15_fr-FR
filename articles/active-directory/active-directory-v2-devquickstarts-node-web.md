<properties
    pageTitle="Azure AD version 2.0 NodeJS Web application | Microsoft Azure"
    description="Découvrez comment créer une application web nœud JS qui vous permet de comptes d’utilisateurs avec les deux Account Microsoft personnel et Professionnel ou scolaire."
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-a-nodejs-web-app"></a>Ajouter de se connecter à un nodeJS Web App


> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).


Ici, nous allons utiliser Passport pour :

- Connecter l’utilisateur à l’application à l’aide d’Azure AD et l’extrémité de la version 2.0.
- Afficher des informations sur l’utilisateur.
- Connectez-vous à l’utilisateur se déconnecter de l’application.

**Passport** est authentification logiciels intermédiaires Node.js. Extrêmement flexible et modulaire, Passport peut être de manière transparente déplacé à tout basée sur Express ou Resitify application web. Un ensemble complet de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter, etc.. Nous avons développé une stratégie pour Microsoft Azure Active Directory. Nous seront installez ce module, puis ajoutez Microsoft Azure Active Directory `passport-azure-ad` plug-in.

## <a name="download"></a>Télécharger

Le code de ce didacticiel étant conservée [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs).  Pour suivre, vous pouvez [Télécharger structure de l’application comme un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) ou cloner la structure :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

L’application terminée est fournie à la fin de ce didacticiel également.

## <a name="1-register-an-app"></a>1. enregistrer une application
Créer une nouvelle application en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copier vers le bas de l' **Id de l’Application** affecté à votre application, vous en aurez besoin plus rapidement.
- Ajouter la plateforme **Web** pour votre application.
- Entrez l' **URI rediriger**. La redirection URI indique à Azure Active Directory dans lequel les réponses d’authentification doivent être dirigés - la valeur par défaut pour ce didacticiel est `http://localhost:3000/auth/openid/return`.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. ajouter pré-requisities à votre répertoire

À partir de la ligne de commande, accédez à votre dossier racine si ce n’est pas déjà il et exécutez les commandes suivantes :

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- En outre, nous avons utilisé `passport-azure-ad` dans la structure du démarrage rapide.

- `npm install passport-azure-ad`


Les bibliothèques vous installerez dépendent de ce passport azure ad-.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. configurer votre application pour utiliser la stratégie passport-nœud-js
Ici, nous allons configurer logiciels intermédiaires Express pour utiliser le protocole d’authentification OpenID se connecter.  Passport est utilisée pour émettre des demandes de connexion et déconnexion, gérer la session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Pour commencer, ouvrez la `config.js` de fichier dans la racine du projet, puis entrez les valeurs de la configuration de votre application dans le `exports.creds` section.
    -   La `clientID:` est l' **Id de l’Application** affecté à votre application dans le portail d’inscription.
    -   La `returnURL` est l' **Rediriger URI** que vous avez entré dans le portail.
    - La `clientSecret` est le mot de passe que vous avez généré dans le portail.

- Ouvrez ensuite `app.js` à la racine du projet et ajoutez l’appel ci-dessous pour appeler la `OIDCStrategy` stratégie qui est fourni avec`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Ensuite, utilisez la stratégie nous référencées simplement pour gérer les demandes de connexion

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    scope: config.creds.scope
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport utilise un modèle similaire pour tous les qu'il s’agit des stratégies (Twitter, Facebook, etc.) de tous les auteurs de stratégie supplémentaire. Examinez la stratégie voir que nous transmettre un function() qui a un jeton et un terminé en tant que les paramètres. La stratégie sera loyalement revenez à nous une fois que tout il s’agit de travail. Une fois que c’est le cas, nous souhaiterez stocker l’utilisateur et placent le jeton afin que nous n’aurez pas besoin de demander à nouveau.

> [AZURE.IMPORTANT]
Le code ci-dessus vous permet d’accéder aux utilisateurs qui se passe s’authentifier à notre serveur. Il s’agit d’enregistrement automatique. Dans les serveurs de production vous voudrez permettre à tout le monde sans avoir d’abord les passez par un processus d’enregistrement que vous décidez. Il s’agit généralement le modèle que vous voyez dans les applications consommateur qui vous permettent de vous inscrire sur Facebook, mais puis vous demander d’entrer des informations supplémentaires. Si ce n’est pas un exemple d’application, nous pouvons ont venez d’extraire le message électronique à partir de l’objet jeton qui est renvoyée alors invité à entrer des informations supplémentaires. Dans la mesure où il s’agit d’un serveur de test nous suffit de les ajouter à la base de données en mémoire.

- Ensuite, nous allons ajouter les méthodes qui nous permettra d’effectuer le suivi de le connecté dans les utilisateurs requis par Passport. Cela inclut sérialiser et désérialiser des informations de l’utilisateur :

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- Ensuite, nous allons ajouter le code pour charger le moteur express. Vous trouverez nous utilisons la /views par défaut et fournit des motifs de /routes Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Pour finir, nous allons ajouter des itinéraires billet qui transmet les demandes d’ouverture de session réel à la `passport-azure-ad` moteur :

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. Utilisez Passport pour émettre des demandes de connexion et déconnexion à Azure Active Directory

Votre application est désormais correctement configurée pour communiquer avec le point de terminaison version 2.0 à l’aide du protocole d’authentification OpenID se connecter.  `passport-azure-ad`a prise en charge de tous les détails insupportable d’élaboration des messages d’authentification, la validation des jetons à partir d’Azure AD et la maintenance de session utilisateur.  Ne reste plus qu’à permettre à vos utilisateurs de se connecter et se déconnecter recueillir des informations supplémentaires sur l’utilisateur connecté.

- Tout d’abord, vous permet d’ajouter les méthodes par défaut, login, compte et déconnexion à notre `app.js` fichier :

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   Passons en revue en détail :
    -   La `/` itinéraire vous redirige vers l’affichage index.ejs en passant l’utilisateur dans la demande (s’il existe)
    - La `/account` itinéraire sera première ***vous assurer que nous apportions authentifiés*** (nous implémenter qui ci-dessous) puis passer l’utilisateur dans la demande de sorte que nous pouvons obtenir plus d’informations sur l’utilisateur.
    - La `/login` itinéraire appellent notre authentificateur azuread openidconnect de `passport-azuread` et si qui n’aboutit pas will redirigent l’utilisateur vers /login
    - La `/logout` simplement appelle la logout.ejs (et router) qui efface les cookies et puis revenez l’utilisateur à la index.ejs


- Pour la dernière partie de `app.js`, nous allons ajouter la méthode EnsureAuthenticated qui est utilisée dans `/account` ci-dessus.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- Pour finir, nous allons créer le serveur lui-même dans `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. créer des affichages et des itinéraires dans express pour afficher notre utilisateur dans le site Web

Nous avons notre `app.js` complète. À présent, nous devons simplement ajouter les parcours et les vues affichant les informations nous accéder à l’utilisateur, ainsi que la poignée de la `/logout` et `/login` itinéraires nous avons créé.

- Créer la `/routes/index.js` itinéraire sous le répertoire racine.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Créer la `/routes/user.js` itinéraire sous le répertoire racine

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Ces itinéraires simples passera simplement le long de la demande à notre vues, y compris l’utilisateur, le cas échéant.

- Créer la `/views/index.ejs` affichage sous le répertoire racine. Il s’agit d’une page simple qui vous appelez nos méthodes de connexion et de déconnexion et nous permettent de récupérer les informations de compte. Veuillez noter que nous pouvons conditionnel `if (!user)` en tant que l’utilisateur en cours est passé dans la demande est preuve nous avons connecté dans l’utilisateur.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- Créer la `/views/account.ejs` afficher sous le répertoire racine afin que nous puissions afficher des informations supplémentaires qui `passport-azuread` a placé dans la demande de l’utilisateur.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Pour finir, nous allons donner l’impression d’assez en ajoutant une mise en page. Créer la ' / affichage des views/layout.ejs sous le répertoire racine

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> |
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> |
            <a href="/account">Account</a> |
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Enfin, créer et exécuter votre application !

Exécuter `node app.js` et accédez à`http://localhost:3000`


Connectez-vous à l’aide d’un Account Microsoft ou un compte professionnel ou scolaire et notez l’identité de l’utilisateur est pris en compte dans la liste Account.  Vous avez à présent une application web sécurisée à l’aide des protocoles standard qui peuvent authentifier les utilisateurs avec leurs comptes personnels et Professionnel ou scolaire.

##<a name="next-steps"></a>Étapes suivantes

Pour référence, l’exemple terminé (sans vos valeurs configuration) [est fourni sous forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip), ou vous pouvez cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Vous pouvez désormais déplacer vers des rubriques plus avancées.  Vous souhaiterez peut-être essayer :

[Sécuriser une node.js web api en utilisant le point de terminaison version 2.0 >>](active-directory-v2-devquickstarts-node-api.md)

Pour des ressources supplémentaires, consultez :
- [Le guide du développeur version 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Balise StackOverflow « azure active directory » >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et l’abonnement aux alertes avis de sécurité.
