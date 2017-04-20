<properties
    pageTitle="Azure AD NodeJS mise en route | Microsoft Azure"
    description="Découvrez comment créer un API Web REST Node.js qui s’intègre à Azure AD pour l’authentification."
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

# <a name="getting-started-with-web-api-for-node"></a>Prise en main avec l’API WEB pour nœud

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** est authentification logiciels intermédiaires Node.js. Extrêmement flexible et modulaire, Passport peut être de manière transparente déplacé à tout basée sur Express ou Resitify application web. Un ensemble complet de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter, etc.. Nous avons développé une stratégie pour Microsoft Azure Active Directory. Nous seront installez ce module, puis ajoutez Microsoft Azure Active Directory `passport-azure-ad` plug-in.

Pour ce faire, vous devez :

1. Enregistrer une application avec Azure Active Directory
2. Configurer votre application pour utiliser azure ad-passport de Passport plug-in.
3. Configurer une application cliente pour appeler l’à faire liste API Web

Le code de ce didacticiel étant conservée [sur GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [AZURE.NOTE] Cet article n’aborde pas la mise en œuvre se connecter, d’abonnement et gestion des profils avec Azure AD B2C.  Il se concentre sur appel web API une fois que l’utilisateur est déjà authentifié.  Si vous n’avez pas déjà, vous devez commencer par [l’intégration avec Azure Active Directory document](./develop/active-directory-how-to-integrate.md) pour en savoir plus sur les concepts de base d’Azure Active Directory.


Nous avez publié tout le code source pour cet exemple en cours d’exécution dans GitHub sous une licence MIT, donc n’hésitez pas à cloner (ou mieux encore, branche !) et fournir des commentaires et extraire des requêtes.

## <a name="about-nodejs-modules"></a>À propos des Modules Node.js

Dans cette procédure, nous allons utiliser Node.js modules. Modules sont chargeables JavaScript qui fournissent des fonctionnalités spécifiques de votre application. Vous installez généralement modules à l’aide de l’outil de ligne de commande Node.js NPM dans le répertoire d’installation NPM, mais certains modules, tels que le module HTTP, sont inclus le package Node.js core.
Modules installés sont enregistrées dans le répertoire node_modules à la racine de votre répertoire d’installation Node.js. Chaque module dans le répertoire node_modules conserve son propre répertoire node_modules qui contient tous les modules dont elle dépend, et chaque module requis a un répertoire node_modules. Cette structure de répertoire récursive représente la chaîne de dépendance.

Résultats de cette structure de chaîne de dépendance dans un encombrement application plus important, mais elle garantit que toutes les dépendances sont remplies et que la version des modules utilisées dans le développement sera également utilisée en production. Cela rend le comportement de l’application de production plus prévisibles et empêche les problèmes de contrôle de version qui peuvent affecter les utilisateurs.

## <a name="1-register-a-azure-ad-tenant"></a>1. enregistrer un client AD Azure

Pour utiliser cet exemple, vous devrez un client annuaire Active Azure. Si vous ne savez pas quel un client est ou la façon dont vous souhaitez obtenir un, voir [Comment faire pour obtenir une Azure Active Directory du client](active-directory-howto-tenant.md).

## <a name="2-create-an-application"></a>2. création d’une application

À présent, vous devez créer une application dans votre annuaire, ce qui donne Azure AD certaines informations qu’il souhaite communiquer en toute sécurité avec votre application.  L’application cliente et l’API web seront représentés par un seul **ID de l’Application** dans ce cas, dans la mesure où ils comprennent une application logique.  Pour créer une application, suivez [ces instructions](active-directory-how-applications-are-added.md). Si vous créez une application métier [ces instructions supplémentaires peuvent vous être utiles](active-directory-applications-guiding-developers-for-lob-applications.md).

N’oubliez pas :

- Se connecter au portail de gestion Azure.
- Dans le volet de navigation gauche, cliquez sur **Active Directory**.
- Sélectionnez le client de l’endroit où vous souhaitez enregistrer l’application.
- Cliquez sur l’onglet **Applications** , et cliquez sur Ajouter dans le bac d’alimentation bas.
- Suivez les invites et créez une nouvelle **Application Web et/ou WebAPI**.
    - Le **nom** de l’application décrira votre application aux utilisateurs finaux
    - L' **Authentification URL** est l’URL de base de votre application.  Valeur par défaut du code de l’exemple est `https://localhost:8080`.
    - L' **Application ID URI** est un identificateur unique pour votre application.  La convention consiste à utiliser `https://<tenant-domain>/<app-name>`, par exemple :`https://contoso.onmicrosoft.com/my-first-aad-app`
- Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client.  Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet Configurer.

- RAPPEL : créer une **Application Secret** pour votre application et le copier vers le bas.  Vous en aurez besoin peu de temps.
- RAPPEL : Copier vers le bas de l' **ID de l’Application** qui est affectée à votre application.  Vous devez également il peu de temps.


## <a name="3-download-nodejs-for-your-platform"></a>3. Téléchargez node.js pour votre plateforme
Pour pouvoir utiliser cet exemple, vous devez disposer d’une installation active de Node.js.

Installez Node.js à partir de [http://nodejs.org](http://nodejs.org).

## <a name="4-install-mongodb-on-to-your-platform"></a>4. Installer MongoDB à votre plateforme

Pour pouvoir utiliser cet exemple, vous devez disposer d’une installation active de MongoDB. Nous allons utiliser MongoDB afin de rendre notre permanente API REST entre instances de serveur.

Installez MongoDB à partir de [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Cette procédure suppose que vous utilisez les extrémités de l’installation et le serveur par défaut pour MongoDB, c'est-à-dire au moment de la rédaction de ce document : mongodb://localhost


## <a name="5-install-the-restify-modules-in-to-your-web-api"></a>5. installer les modules Restify à votre API Web

Nous allons utiliser Resitfy pour générer notre API REST. Restify est une infrastructure d’application Node.js minimale et flexible dérivée de Express qui contient un ensemble robuste de fonctionnalités pour la création d’API REST en haut de la connexion.

### <a name="install-restify"></a>Installer Restify

À partir de la ligne de commande, accédez au répertoire azuread. Si le répertoire **azuread** n’existe pas, créez-le.

`cd azuread - or- mkdir azuread; cd azuread`

Tapez la commande suivante :

`npm install restify`

Cette commande installe Restify.

#### <a name="did-you-get-an-error"></a>SI VOUS AVEZ REÇU UN MESSAGE D’ERREUR ?

Lorsque vous utilisez npm sur certains systèmes d’exploitation, vous pouvez recevoir une erreur d’erreur : EPERM, chmod ' / usr/local/bin / … » et une requête à essayer d’exécuter le compte en tant qu’administrateur. Dans ce cas, utilisez la commande sudo pour exécuter npm à un niveau de privilège supérieur.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>SI VOUS AVEZ REÇU UN MESSAGE D’ERREUR CONCERNANT DTRACE ?

Vous pouvez voir ressembler à ceci lorsque vous installez Restify :

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```


Restify fournit un mécanisme puissant pour effectuer le suivi des appels de reste à l’aide de DTrace. Toutefois, de nombreux systèmes d’exploitation n’ont pas DTrace disponible. Vous pouvez ignorer ces erreurs.


Le résultat de cette commande doit ressembler à ce qui suit :


    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="6-install-passportjs-in-to-your-web-api"></a>6. Installer Passport.js dans à votre site Web API

[Passport](http://passportjs.org/) est authentification logiciels intermédiaires Node.js. Extrêmement flexible et modulaire, Passport peut être de manière transparente déplacé à tout basée sur Express ou Resitify application web. Un ensemble complet de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et mot de passe, Facebook, Twitter, etc.. Nous avons développé une stratégie pour Azure Active Directory. Nous s’installer ce module, puis ajoutez la stratégie Azure Active Directory plug-in.

À partir de la ligne de commande, accédez au répertoire azuread.

Entrez la commande suivante pour installer passport.js

`npm install passport`

Le résultat de la commande doit ressembler à ce qui suit :

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="7-add-passport-azure-ad-to-your-web-api"></a>7. Ajoutez Passport Azure AD à votre site Web API

Ensuite, nous allons ajouter la stratégie OAuth, à l’aide de passport-azuread, une gamme de stratégies qui se connectent Azure Active Directory avec Passport. Nous allons utiliser cette stratégie pour les jetons porteur dans cet exemple API Rest.

> [AZURE.NOTE] Bien qu’oauth2 ne fournit une infrastructure dans lequel n’importe quel type de jeton connu peut être émis, uniquement certains types de jetons maîtriser utilisation poussée. Pour la protection des points de terminaison, qui a désactivé l’être porteur jetons. Jetons PORTEUR sont les plus largement émis type du jeton de dans oauth2 n’et nombreuses mises en œuvre part du principe que porteur jetons sont le seul type de jeton émis.

À partir de la ligne de commande, accédez au répertoire azuread

Tapez la commande suivante pour installer le module de passport azure ad Passport.js :

`npm install passport-azure-ad`

Le résultat de la commande doit ressembler à ce qui suit :

    ``
    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## <a name="8-add-mongodb-modules-to-your-web-api"></a>8. Ajoutez MongoDB modules à votre API Web

Nous allons utiliser MongoDB comme notre magasin de données pour cette raison, nous devons installer à la fois les largement utilisées plug-in pour gérer les modèles et schémas appelée Mongoose, ainsi que le pilote de base de données pour MongoDB, également appelée MongoDB.


* `npm install mongoose`

## <a name="9--install-additional-modules"></a>9. installer des modules complémentaires

Ensuite, nous allons installer les modules requis restants.


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`


Entrez les commandes suivantes pour installer les modules suivants dans votre annuaire node_modules :

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## <a name="10-create-a-serverjs-with-your-dependencies"></a>10. créer un server.js avec vos dépendances

Le fichier server.js fournira la majorité de notre fonctionnalité pour notre serveur API Web. Nous ajouterons la plupart des notre code à ce fichier. À des fins de production vous feriez refactoriser les fonctionnalités dans des fichiers plus petits, par exemple contrôleurs et des itinéraires différents. Aux fins de cette démonstration, nous allons utiliser server.js pour cette fonctionnalité.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Créer un `server.js` dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
    'use strict';

    /**
    * Module dependencies.
    */

    var fs = require('fs');
    var path = require('path');
    var util = require('util');
    var assert = require('assert-plus');
    var bunyan = require('bunyan');
    var getopt = require('posix-getopt');
    var mongoose = require('mongoose/');
    var restify = require('restify');
    var passport = require('passport');
  var BearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Enregistrez le fichier. Nous renverra lui peu de temps.

## <a name="11-create-a-config-file-to-store-your-azure-ad-settings"></a>11 :. Créer un fichier de configuration pour stocker vos paramètres Azure AD

Ce fichier de code passe les paramètres de configuration de votre portail annuaire Active Azure à Passport.js. Vous avez créé ces valeurs de configuration lors de l’ajout de l’API Web au portail dans la première partie de la procédure. Nous allons expliquer les éléments à ajouter dans les valeurs de ces paramètres une fois que vous avez copié le code.


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Créer un `config.js` dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
 exports.creds = {
     mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
     clientID: 'your client ID',
     audience: 'your application URL',
    // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
  // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
     identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
     validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
     passReqToCallback: false,
     loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

 };


```
Enregistrez le fichier.

## <a name="12-add-configuration-to-your-serverjs-file"></a>12. Ajouter configuration à votre fichier de server.js

Nous avons besoin de lire ces valeurs à partir du fichier de configuration de la que vous venez de créer dans notre application. Pour ce faire, nous avons simplement ajouter le fichier .config comme une ressource requise dans notre application et définissez les variables globales à celles figurant dans le document config.js

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Ouvrir votre `server.js` dans notre éditeur favori et ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ensuite, ajoutez une nouvelle section `server.js` par le code suivant :

```Javascript
var options = {
    // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback,
    loggingLevel: config.creds.loggingLevel

};

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
var log = bunyan.createLogger({
    name: 'Azure Active Directory Bearer Sample',
         streams: [
        {
            stream: process.stderr,
            level: "error",
            name: "error"
        },
        {
            stream: process.stdout,
            level: "warn",
            name: "console"
        }, ]
});

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

Enregistrez le fichier.



## <a name="13-add-the-mongodb-model-and-schema-information-using-moongoose"></a>13. ajouter le modèle de MongoDB et les informations du schéma avec Moongoose

Toute cette préparation va maintenant pour démarrer rembourser que nous fermes ensemble ces trois fichiers dans un service API REST.

Pour cette procédure pas à pas nous utiliserons MongoDB pour stocker notre tâches comme indiqué à ***l’étape 4***.

Si vous rappelez à partir de la `config.js` fichier que nous avons créé à ***l’étape 11*** nous avons appelé notre base de données `tasklist` comme c’est ce que nous placer à la fin de notre URL de connexion mogoose_auth_local. Vous n’êtes pas obligé de créer cette base de données à l’avance dans MongoDB, il crée cela pour que nous puissions à la première exécution de notre application server (en supposant qu’il n’existe pas déjà).

À présent que nous avons dit le serveur quelle base de données MongoDB nous aimerions à utiliser, nous devons écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches de notre serveur.

#### <a name="discussion-of-the-model"></a>Discussion du modèle

Notre modèle de schéma est très simple, et vous développez selon les besoins.

NAME : le nom de qui est affectée à la tâche. Une ***chaîne***

TÂCHE - la tâche elle-même. Une ***chaîne***

DATE - la date d’échéance de la tâche. ***Date/heure***

Terminer - si la tâche est terminée ou non. ***BOOLÉENNE***

#### <a name="creating-the-schema-in-the-code"></a>Création du schéma dans le code


À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Ouvrir votre `server.js` dans notre éditeur favori et ajoutez les informations suivantes en dessous de l’entrée de configuration :

```Javascript
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    task: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Comme vous pouvez voir à partir du code, nous créons notre schéma et puis créer un objet de modèle que nous utiliserons pour stocker nos données dans l’ensemble du code lorsque nous définissez notre ***itinéraires***.

## <a name="14-add-our-routes-for-our-task-rest-api-server"></a>14. ajouter notre itinéraires pour notre serveur API REST de tâche

Maintenant que nous disposons d’un modèle de base de données pour l’utiliser avec, nous allons ajouter les itinéraires que nous utiliserons pour notre serveur API REST.

### <a name="about-routes-in-restify"></a>À propos des itinéraires dans Restify

Fonctionnement des gammes Restify dans la même façon qu’il faire à l’aide de la pile Express. Vous définissez des itinéraires à l’aide de l’URI que vous attendez les applications clientes à appeler. En règle générale, vous définissez votre parcours dans un fichier distinct. Dans notre cas, nous allons lui ajouter notre itinéraires dans le fichier server.js. Nous vous recommandons de que vous prendre leur propre fichier pour une utilisation de production.

Un modèle par défaut pour un itinéraire Restify est la suivante :

```Javascript
function createObject(req, res, next) {

// do work on Object

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // keep the server going
}

....

server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.

```


Voici le modèle au niveau de la plus simple. Resitfy (et Express) fournissent beaucoup plus approfondie functionaltiy telles que la définition des types d’applications et la réalisation routage complexes entre différents points de terminaison. Dans notre cas, nous allons conserver ces itinéraires très simplement.

### <a name="1-add-default-routes-to-our-server"></a>1. Ajoutez itinéraires par défaut à notre serveur

Nous seront désormais ajouter les itinéraires CRUD base de créer, récupérer, mettre à jour et supprimer.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

Ouvrir votre `server.js` dans notre éditeur favori et ajoutez les informations suivantes sous les entrées de base de données que vous avez effectuées ci-dessus :

```Javascript

/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### <a name="2-next-lets-add-some-error-handling-in-our-apis"></a>2. suivant, nous allons ajouter quelques gestion des erreurs dans notre API :

```

///--- Errors for communicating something interesting back to the client

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="15-create-your-server"></a>15. créer votre serveur !

Nous avons notre base de données défini, nous avons notre itinéraires en place, et la dernière chose à faire est d’ajouter notre instance du serveur qui gérera nos appels.

Restify (et Express) comporte un grand nombre de personnalisation approfondie, vous pouvez faire pour un serveur de l’API REST, mais à nouveau nous allons utiliser la configuration de base plus notre fins.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
```

## <a name="16-adding-the-routes-to-the-server-without-authentication-for-now"></a>16. Ajout itinéraires sur le serveur (sans authentification pour l’instant)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## <a name="17-before-we-add-oauth-support-lets-run-the-server"></a>17. avant d’ajouter support OAuth, nous allons exécuter le serveur.

Tester votre serveur avant d’ajouter l’authentification

Le plus simple consiste à l’aide d’ourlée dans une ligne de commande. Avant cela, nous avons besoin d’un utilitaire simple qui nous permettent d’analyser sortie en tant que JSON. Pour cela, installez l’outil json que tous les exemples ci-dessous qui utilisent.

`$npm install -g jsontool`

Cette procédure installe l’outil JSON globalement. À présent que nous avons accompli qui – nous allons jouer avec le serveur :

Tout d’abord, assurez-vous que votre instance monogoDB est en cours d’exécution...

`$sudo mongod`

Ensuite, accédez au répertoire et démarrer ondulation...

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Ensuite, nous pouvons ajouter une tâche de cette façon :

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

La réponse doit être :

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Et nous pouvons de la liste tâches pour Brandon ainsi :

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si cela fonctionne, nous sommes prêts à ajouter OAuth au serveur API REST.

**Vous disposez d’un serveur de l’API REST avec MongoDB !**


## <a name="18-add-authentication-to-our-rest-api-server"></a>18. ajouter une authentification à notre serveur API REST

Maintenant que nous avons une API REST en cours d’exécution (Félicitations, btw !), nous allons apprendre pour qu’il soit utile contre Azure AD.

À partir de la ligne de commande, accédez au dossier **azuread** si ce n’est pas déjà il :

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1 : utiliser la OIDCBearerStrategy qui est inclus avec passport azure ad

Jusqu'à présent, nous avons un serveur reste TODO classique sans tout type d’autorisation. C’est l’endroit où nous démarrez qui assembler.

Tout d’abord, nous avons besoin indiquer que nous voulons utiliser Passport. Placer ce droit après la configuration du serveur :

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Lors de la rédaction API vous devez toujours lier les données à un nom unique à partir du jeton de l’utilisateur ne peut pas usurpation. Lorsque ce serveur stocke les tâches, il stocke les en fonction de l’ID d’objet de l’utilisateur dans le jeton (appelé via token.oid) qui nous placer dans le champ « propriétaire ». Ainsi, seul cet utilisateur peut accéder à son emplacement et personne d’autre ne peut accéder à l’emplacement entré. Il n’existe aucune exposition dans l’API de « propriétaire » pour un utilisateur externe peut demander l’autre emplacement même s’ils sont authentifiés.

Ensuite, nous allons utiliser la stratégie porteur qui est fourni avec passport azure ad. Regardez simplement le code pour le moment, je vais l’expliquer peu de temps. Placer ce après ce que vous pated ci-dessus :

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/

var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.sub === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var bearerStrategy = new BearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                users.push(token);
                owner = token.sub;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(bearerStrategy);
```

Passport utilise un modèle similaire pour tous les qu'il s’agit des stratégies (Twitter, Facebook, etc.) de tous les auteurs de stratégie supplémentaire. Examinez la stratégie voir que nous transmettre un function() qui a un jeton et un terminé en tant que les paramètres. La stratégie sera loyalement revenez à nous une fois que tout il s’agit de travail. Une fois que c’est le cas, nous souhaiterez stocker l’utilisateur et placent le jeton afin que nous n’aurez pas besoin de demander à nouveau.

> [AZURE.IMPORTANT]
Le code ci-dessus vous permet d’accéder aux utilisateurs qui se passe s’authentifier à notre serveur. Il s’agit d’enregistrement automatique. Dans les serveurs de production vous voudrez permettre à tout le monde sans avoir d’abord les passez par un processus d’enregistrement que vous décidez. Il s’agit généralement le modèle que vous voyez dans les applications consommateur qui vous permettent de vous inscrire sur Facebook, mais puis vous demander d’entrer des informations supplémentaires. Si ce n’est pas un programme de ligne de commande, nous pouvons ont venez d’extraire le message électronique à partir de l’objet jeton qui est renvoyée alors invité à entrer des informations supplémentaires. Dans la mesure où il s’agit d’un serveur de test nous suffit de les ajouter à la base de données en mémoire.

### <a name="2-finally-protect-some-endpoints"></a>2. pour finir, protéger des points de terminaison

Vous protégez des points de terminaison en spécifiant la `passport.authenticate()` appeler avec le protocole que vous souhaitez utiliser.

Nous allons modifier notre itinéraire dans notre code serveur faire plus intéressant :

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="19-run-your-server-application-again-and-ensure-it-rejects-you"></a>19. Exécutez à nouveau votre application serveur et assurez-vous que celui-ci vous refuse

Utiliser la fonctionnalité `curl` à nouveau pour voir si nous ont désormais oauth2 ne protection contre nos points de terminaison. Nous fera avant ouverte un de nos clients SDK par rapport à ce point de terminaison. Les en-têtes de lignes retournés doivent être suffisant pour dire nous que nous sommes vers le bas la bonne voie.

Tout d’abord, assurez-vous que votre instance monogoDB est en cours d’exécution :

  $sudo mongod

Ensuite, accédez au répertoire et démarrer ondulation...

  $ cd azuread $ nœud server.js

Essayez un billet de base :

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Un 401 est la réponse que vous recherchez ici, car cela indique que le calque Passport tente de rediriger vers le point de terminaison autoriser, qui est exactement ce que vous voulez.

## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>Félicitations ! Vous avez un Service de l’API REST en utilisant oauth2 ne !

Vous avez problème comme vous le pouvez avec ce serveur sans l’aide d’un client compatible oauth2 ne. Vous devez suivre une procédure pas à pas supplémentaires.

Si vous recherchiez uniquement les informations sur la mise en œuvre d’un API REST à l’aide de Restify et oauth2 ne, vous avez plus de suffisamment de code pour conserver développez votre service et apprendre à créer dans cet exemple.

Si vous êtes intéressé par la procédure suivante dans votre parcours ADAL, voici quelques clients ADAL pris en charge, nous vous recommandons de vous permettant de continuer à travailler :

Simplement cloner vers le bas jusqu'à votre ordinateur pour les développeurs et configurez comme indiqué dans la procédure pas à pas.

[Terme ADAL pour iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
