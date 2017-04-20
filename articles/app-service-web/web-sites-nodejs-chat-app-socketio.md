<properties
    pageTitle="Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure"
    description="Didacticiel illustrant l’utilisation de socket.io dans une application web node.js hébergée sur Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure

Socket.IO fournit la communication en temps réel entre votre serveur node.js et vos clients à l’aide de WebSocket. Il prend également en charge secours aux autres transports (par exemple, l’interrogation long) qui fonctionnent avec les navigateurs plus anciens. Ce didacticiel vous guident dans une application de conversation Socket.IO basé comme une application web Azure d’hébergement et vous montrent comment mettre à l’échelle de l’application à l’aide de [Cache Redis Azure]. Pour plus d’informations sur Socket.IO, voir <http://socket.io/>.

> [AZURE.NOTE] Les procédures décrites dans cette tâche s’appliquent à l' [Application de Service Web Apps]; pour les Services en nuage, voir [créer une Application de conversation Node.js avec Socket.IO sur un Service Cloud Azure].

## <a name="download-the-chat-example"></a>Télécharger l’exemple de conversation

Pour ce projet, nous allons utiliser l’exemple de conversation à partir du [référentiel Socket.IO GitHub]. Effectuez les opérations suivantes pour télécharger l’exemple et ajoutez au projet que vous avez créé précédemment.

1.  Télécharger un [ZIP ou GZ archivés release] du projet Socket.IO (version 1.3.5 a été utilisée pour ce document)

1.  Extraire l’archive et copier le **exemples\\conversation** répertoire vers un nouvel emplacement. Par exemple, ** \\nœud\\conversation**.

## <a name="modify-appjs-and-install-modules"></a>Modifier app.js et installer des modules

1.  Renommez le fichier **index.js** **app.js**. Cela permet de déterminer qu’il s’agit d’une application Node.js Azure.

1.  Ouvrez le fichier **app.js** dans un éditeur de texte. Modifier la ligne contenant `var io = require('../..')(server);` comme indiqué ci-dessous :

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. Ouvrez le fichier **package.json** et ajoutez une référence à socket.io sous `dependencies`, comme indiqué ci-dessous :

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. À partir de la ligne de commande, placez-vous la ** \\nœud\\conversation** npm répertoire et les utiliser pour installer les modules requis par cette application :

        npm install

    Les modules seront installés dans un sous-dossier nommé **node_modules**.

## <a name="create-an-azure-web-app"></a>Créer une application Web Azure

Suivez ces étapes pour créer une application web Azure, activer la publication Git et activer la prise en charge WebSocket pour l’application web.

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure la version d’évaluation gratuite</a>.

1. Installez l’Interface de ligne Azure (commande Azure) et connectez-vous à votre abonnement Azure. Voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md).

1. S’il s’agit de votre première heure configurer un référentiel dans Azure, vous devez créer des informations d’identification. Depuis l’interface Azure, entrez la commande suivante :

        azure site deployment user set [username] [password]

1. Changer la ** \\node\chat** répertoire et utilisez la commande suivante pour créer un nouveau Azure web app et un référentiel Git local. Cette commande crée également un nommé distant Git « azure ».

        azure site create mysitename --git

    Vous devez remplacer « mysitename » par un nom unique pour votre application web.

1. Validez les fichiers existants vers le référentiel local en utilisant les commandes suivantes :

        git add .
        git commit -m "Initial commit"

1. Distribuer les fichiers vers le référentiel Azure Web Apps avec la commande suivante :

        git push azure master

    Lorsque vous y êtes invité, entrez vos informations d’identification de l’étape 2. Vous recevrez les messages d’état comme modules sont importées sur le serveur. Une fois ce processus terminé, l’application sera hébergée sur votre application web Azure.

    > [AZURE.NOTE] Pendant l’installation du module, vous remarquerez peut-être des erreurs que « le projet importé... non trouvé ». Il peuvent être ignorés.

1. Socket.IO utilise, selon toute évidence qui n’est pas activés par défaut sur Azure. Pour activer sockets web, utilisez la commande suivante :

        azure site set -w

    Si vous y êtes invité, entrez le nom de l’application web.

    >[AZURE.NOTE]
    >Le « site azure set -w » commande est uniquement avec la version 0.7.4 ou une version ultérieure de l’Interface de ligne de commande Azure. Vous pouvez également activer la prise en charge WebSocket à l’aide du [Portail Azure](https://portal.azure.com).
    >
    >Pour activer WebSocket à l’aide du portail Azure, cliquez sur l’application web à partir de la carte Web Apps, cliquez sur **tous les paramètres** > **paramètres de l’Application**. Sous **Web Sockets**, cliquez **sur**. Cliquez sur **Enregistrer**.

1. Pour afficher l’application web sur Azure, utilisez la commande suivante pour lancer votre navigateur web et accédez à l’application web hébergé :

        azure site browse

Votre application est en cours d’exécution sur Azure et pouvez relais des messages de conversation entre différents clients à l’aide de Socket.IO.

## <a name="scale-out"></a>Évoluer

Applications Socket.IO peuvent faire évoluer à l’aide d’une __carte__ pour distribuer des messages et les événements entre plusieurs instances de l’application. Il existe plusieurs cartes disponible, la carte [socket.io redis] peut servir facilement grâce à la fonctionnalité de Cache Redis Azure.

> [AZURE.NOTE] Une condition supplémentaire pour le redimensionnement d’une solution Socket.IO est prise en charge des sessions permanentes. Sessions permanentes sont activées par défaut pour les applications Web de Azure Azure demander routage. Pour plus d’informations, voir [Instance affinité dans des Sites Web Azure].

### <a name="create-a-redis-cache"></a>Créer un cache Redis

Effectuez les étapes décrites dans [créer un cache dans Azure Redis Cache] pour créer un cache.

> [AZURE.NOTE] Enregistrer le __nom d’hôte__ et la __clé primaire__ pour le cache, comme il seront nécessaire dans les étapes suivantes.

### <a name="add-the-redis-and-socketio-redis-modules"></a>Ajouter les modules de socket.io redis et redis

1. À partir d’une ligne de commande, placez-vous la __ \\nœud\\conversation__ répertoire et utilisez la commande suivante.

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] Les versions spécifiées dans cette commande sont les versions utilisées lors du test de cet article.

1. Modifier le fichier __app.js__ pour ajouter ce qui suit immédiatement après les lignes`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Remplacez __redishostname__ et __rediskey__ par le nom d’hôte et la clé pour le cache de résolution Redis.

    Cela créer une publication et s’abonner client dans le cache Redis créé précédemment. Les clients sont ensuite utilisés avec la carte pour configurer Socket.IO pour utiliser le cache Redis pour passer des messages et les événements entre les instances de votre application

    > [AZURE.NOTE] Tandis que la carte __socket.io redis__ peut communiquer directement à Redis, la version actuelle n’autorise pas l’authentification requise par le cache Azure Redis. Si la connexion initiale est créée en utilisant le module __redis__ , puis le client est passé à la carte __socket.io redis__ .
    >
    > Azure Redis Cache prend en charge les connexions sécurisées à l’aide de port 6380, les modules utilisés dans cet exemple ne prend pas en charge des connexions sécurisées telles qu’elles 7/14/2014. Le code ci-dessus utilise le par défaut, le port non sécurisé de 6379.

1. Enregistrer modification __app.js__

### <a name="commit-changes-and-redeploy"></a>Valider les modifications et redéployez

À partir de la ligne de commande dans le __ \\nœud\\conversation__ répertoire, utilisez les commandes suivantes pour valider les modifications et redéployez l’application.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Une fois que les modifications ont été diffusées sur le serveur, vous pouvez optimiser votre site entre plusieurs instances à l’aide de la commande suivante.

    azure site scale instances --instances #

Où __#__ est le nombre d’instances à créer.

Vous pouvez vous connecter à votre application web à partir de plusieurs navigateurs ou ordinateurs pour vérifier que les messages sont envoyés correctement à tous les clients.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="connection-limits"></a>Limites de connexion

Applications Web Azure est disponible dans plusieurs références SKU, qui déterminent les ressources disponibles pour votre site. Cela inclut le nombre de connexions WebSocket autorisées. Pour plus d’informations, consultez la [page de prix des applications Web].

### <a name="messages-arent-being-sent-using-websockets"></a>Messages ne sont pas envoyés à l’aide de WebSocket

Si les navigateurs clients conservent revenant à durée d’interrogation au lieu d’utiliser WebSocket, il peut être en raison d’une des opérations suivantes.

* **Essayez en limitant le transport WebSocket uniquement**

    Pour Socket.IO à utiliser WebSocket comme le transport des messages, le serveur et le client doivent prendre en charge WebSocket. Si un ou l’autre n’existe pas, Socket.IO négociera un autre transport, telles que l’interrogation longue. La liste par défaut des transports utilisé par Socket.IO est ` websocket, htmlfile, xhr-polling, jsonp-polling`. Vous pouvez forcer à utiliser uniquement WebSocket en ajoutant le code suivant dans le fichier **app.js** , après la ligne contenant `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] Notez que les anciens navigateurs qui ne prennent pas en charge WebSocket ne pourront pas vous connecter au site alors que le code ci-dessus est actif, il restreint les communications à WebSocket uniquement.

* **Utiliser le protocole SSL**

    WebSocket repose sur certains en-têtes HTTP utilisés moindre, telles que l’en-tête **de mise à niveau** . Certains appareils réseau intermédiaires, tels que les proxys web, risque de supprimer ces en-têtes. Pour éviter ce problème, vous pouvez établir la connexion WebSocket via le protocole SSL.

    Un moyen facile d’y parvenir consiste à configurer Socket.IO à `match origin protocol`. Cela indique Socket.IO pour sécuriser la communication WebSocket identique à la demande HTTP/HTTPS d’origine de la page web. Si un navigateur utilise une URL HTTPS pour accéder à votre site Web, les communications WebSocket ultérieures via Socket.IO sera sécurisées via le protocole SSL.

    Pour modifier cet exemple pour activer cette configuration, ajoutez le code suivant dans le fichier **app.js** après la ligne contenant `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **Vérifier les paramètres de web.config**

    Applications web Azure hébergeant des applications Node.js utilisent le fichier **web.config** pour acheminer des demandes entrantes à l’application Node.js. Pour, selon toute évidence pour fonctionner correctement avec les applications Node.js **web.config** doit contenir l’entrée suivante.

        <webSocket enabled="false"/>

    Cette opération désactive le module WebSocket IIS, qui inclut sa propre implémentation de WebSocket et est en conflit avec des modules WebSocket spécifiques Node.js comme Socket.IO. Si cette ligne ne figure pas ou est définie sur `true`, cela peut être la raison pour laquelle le transport WebSocket ne fonctionne pas pour votre application.

    En règle générale, les applications Node.js n’incluent pas un fichier **web.config** , afin que les sites Web Azure génère automatiquement un pour les applications Node.js lorsqu’ils sont déployés. Étant donné que ce fichier est généré automatiquement sur le serveur, vous devez utiliser le FTP ou l’URL envoie via FTP pour votre site Web pour afficher ce fichier. Vous trouverez les FTP et les envoie via FTP des URL de votre site Web dans le portail classique en sélectionnant votre application web, puis sur le lien de **tableau de bord** . L’URL sont affichent dans la section **coup de œil** .

    > [AZURE.NOTE] Le fichier **web.config** est généré uniquement par des sites Web Azure si votre application ne fournit pas une. Si vous fournissez un fichier **web.config** dans la racine de votre projet d’application, il sera utilisé par les applications Web Azure.

    Si l’entrée n’est pas présente ou est définie sur une valeur de `true`, puis créez une **web.config** à la racine de votre application Node.js et spécifiez une valeur de `false`.  Pour référence, le Voici une valeur par défaut **web.config** pour une application qui utilise **app.js** comme point d’entrée.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    Si votre application utilise un point d’entrée autre que **app.js**, vous devez remplacer toutes les occurrences de **app.js** par le point d’entrée correct. Par exemple, en remplaçant **app.js** par **server.js**.

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application], où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment créer une application de conversation hébergée dans une application web Azure. Vous pouvez également héberger cette application comme un Service Cloud Azure. Pour savoir comment procéder, voir [créer une Application de conversation Node.js avec Socket.IO sur un Service Cloud Azure].

Pour plus d’informations, voir également le [Centre de développement Node.js].

## <a name="whats-changed"></a>Ce qui a changé

* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants].

<!-- URL List -->

[Cache Redis Azure]: /documentation/services/redis-cache/
[Application Service Web Apps]: http://go.microsoft.com/fwlink/?LinkId=529714
[Page tarifs des applications Web]: http://go.microsoft.com/fwlink/?LinkId=511643
[Créer une Application Node.js conversation avec Socket.IO sur un Service Cloud Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Azure Application Service et son Impact sur les Services Azure existants]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centre de développement Node.js]: /develop/nodejs/
[Essayez de Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751
[Affinité d’instance dans les Sites Web Azure]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Créer un cache dans Azure Redis Cache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[Socket.IO redis]: https://github.com/socketio/socket.io-redis
[Référentiel Socket.IO GitHub]: https://github.com/socketio/socket.io
[ZIP ou GZ archivés release]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
