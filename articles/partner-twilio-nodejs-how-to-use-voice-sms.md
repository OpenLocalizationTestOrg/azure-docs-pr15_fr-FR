<properties 
    pageTitle="À l’aide de Twilio pour, les appels VoIP, messagerie vocale et SMS dans Azure" 
    description="Découvrez comment effectuer un appel téléphonique et envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrits en Node.js." 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>À l’aide de Twilio pour, les appels VoIP, messagerie vocale et SMS dans Azure

Ce guide montre comment créer des applications de communiquer avec Twilio et node.js sur Azure.

<a id="whatis"/>
## <a name="what-is-twilio"></a>Quelles sont les Twilio ?

Twilio est une plateforme API qui rend facile pour les développeurs à passer et recevoir des appels téléphoniques, envoyer et recevoir des messages texte et incorporer appel VoIP dans les applications mobiles natives et celles basées sur le navigateur.  Nous allons brièvement passez en revue comment cela fonctionne avant de plonger.

### <a name="receiving-calls-and-text-messages"></a>Recevoir des appels et des Messages texte

Twilio permet aux développeurs de [numéros de téléphone programmable d’achat] [ purchase_phone] qui peuvent servir à envoyer et recevoir des appels et des messages texte.  Lorsqu’un nombre Twilio reçoit un appel entrant ou du texte, Twilio vous envoie votre application web un HTTP POST ou la requête GET, vous demandant pour obtenir des instructions sur la façon de gérer l’appel ou le texte.  Votre serveur répondra à la demande HTTP de Twilio avec [TwiML][twiml], un ensemble de balises XML qui contiennent des instructions sur la façon de gérer un appel ou du texte simple.  Nous allons voir des exemples de TwiML dans un instant.

### <a name="making-calls-and-sending-text-messages"></a>Émettre des appels et envoyer des Messages texte

En effectuant les requêtes HTTP à l’API de service web Twilio, les développeurs peuvent envoyer des messages texte ou effectuer des appels téléphoniques sortants.  Pour les appels sortants, le développeur doit également spécifier une URL renvoyant instructions TwiML pour savoir comment gérer l’appel sortant une fois qu’il est connecté.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>L’incorporation de fonctionnalités VoIP dans le code de l’interface utilisateur (JavaScript, iOS ou Android)

Twilio fournit un kit de développement côté client peut transformer n’importe quel navigateur web de bureau, application iOS ou Android app en un téléphone VoIP.  Dans cet article, nous allons étudier comment utiliser les appels VoIP appel dans le navigateur.  Outre le Twilio JavaScript Kit de développement en cours d’exécution dans le navigateur, une application côté serveur (notre application node.js) doit être utilisée pour émettre un « jeton de fonctionnalité » au client JavaScript.  Vous pouvez en savoir plus sur l’utilisation de VoIP avec node.js [sur le blog de développement Twilio][voipnode].

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscrivez-vous Twilio (remise Microsoft)

Avant d’utiliser les services Twilio, vous devez première [s’inscrire à un compte][signup].  Les clients Microsoft Azure reçoivent une remise spéciale - [Veillez à s’inscrire ici][signup]!

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>Créez et déployez un site Web Azure node.js

Ensuite, vous devrez créer un site Web node.js s’exécutant sur Azure.  [La documentation officielle pour cela se trouve ici][azure_new_site].  À un niveau élevé, vous allez effectuer les éléments suivants :

* Inscription à un compte Azure, si vous n’en avez pas déjà
* Pour créer un nouveau site Web à l’aide de la console d’administration Azure
* Prise en charge du contrôle Ajout source (nous supposons que vous avez utilisé git)
* Création d’un fichier `server.js` avec une application web node.js simple
* Déploiement d’Azure cette application simple

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>Configurer le Module Twilio

Ensuite, nous va commencer à écrire une application node.js simple qui montre comment utiliser l’API Twilio.  Avant de commencer, nous devons configurer notre Twilio les informations d’identification de compte.  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configuration des informations d’identification Twilio dans les Variables d’environnement système

Afin de pouvoir modifier demandes authentifiés par rapport à la fin précédent Twilio, nous avons besoin de notre compte identificateur de sécurité et auth jeton, quelle fonction sous le nom d’utilisateur et mot de passe défini pour notre compte Twilio. La méthode la plus sûre pour configurer ces pour une utilisation avec le module nœud dans Azure est via variables d’environnement système, vous pouvez définir directement dans la console d’administration Azure.

Sélectionnez votre site Web node.js, puis cliquez sur le lien « Configurer ».  Si vous faites défiler vers le bas, vous verrez une zone dans laquelle vous pouvez définir des propriétés de configuration de votre application.  Entrez vos informations d’identification du compte Twilio ([figurant dans votre tableau de bord Twilio][twilio_dashboard]) comme indiqué - Vérifiez que les nommer « TWILIO_ACCOUNT_SID » et « TWILIO_AUTH_TOKEN », respectivement :

![Console d’administration d’Azure][azure-admin-console]

Une fois que vous avez configuré ces variables, redémarrez votre application dans la console Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Déclaration du module Twilio dans package.json

Ensuite, nous avons besoin de créer un package.json pour gérer les dépendances de notre modules nœud via [npm].  Au même niveau que le fichier « server.js » que vous avez créé dans le didacticiel Azure/node.js, créez un fichier nommé « package.json ».  À l’intérieur de ce fichier, placez les éléments suivants :

  {« nom » : «-nom de l’application », « version » : « 0.0.1 », « privé » : vrai, « scripts » : {« commencer » : « serveur nœud »}, « dépendances » : {« express » : « 3.1.0 », « ejs » : «*», « twilio » : «*»}}

Cela déclare le module twilio comme une dépendance, ainsi que l' [infrastructure web express] populaires[ express] et le moteur de modèle EJS.  OK, maintenant nous avons terminés, nous allons écrire du code !

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>Passer un appel sortant

Créer un formulaire simple qui place un appel vers un numéro que nous avons choisi.  Ouvrez server.js et entrez le code suivant.  Notez l’emplacement indiqué « CHANGE_ME « - y placez le nom de votre site Web azure :

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

Ensuite, créez un répertoire appelé « vues » - dans ce répertoire, créez un fichier nommé « index.ejs » avec le contenu suivant :

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

À présent, déployer votre site Web sur Azure et ouvrez votre domicile.  Vous devriez pouvoir entrer votre numéro de téléphone dans la zone de texte et recevoir un appel à partir de votre numéro de Twilio !

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>Envoyer un Message SMS

À présent, nous allons définir une interface utilisateur et la logique de gestion de formulaires pour envoyer un message texte.  Ouvrir le « server.js » et ajoutez le code suivant après le dernier appel à « app.post » :

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

Dans « views/index.ejs », ajoutez un autre formulaire sous la première personne à soumettre un nombre et un message texte :

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Redéployez votre application sur Azure, et vous devriez maintenant pouvoir soumettre que formulaire et envoyer vous-même (ou un de vos amis le plus proche) un message texte !

<a id="nextsteps"/>
## <a name="next-steps"></a>Étapes suivantes

Vous avez appris maintenant les notions de base de l’utilisation de node.js et Twilio pour créer des applications communiquent.  Mais ces exemples débuts à peine de les possibilités offertes par Twilio et node.js.  Pour plus d’informations à l’aide de Twilio avec node.js, consultez les ressources suivantes :

* [Documents module officiel][docs]
* [Didacticiel sur les systèmes VoIP avec les applications node.js][voipnode]
* [Votr - SMS en temps réel vote application avec node.js et CouchDB (trois parties)][votr]
* [Programmation paire dans le navigateur avec node.js][pair]

Nous espérons que vous appréciez piratage node.js et Twilio sur Azure !

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



