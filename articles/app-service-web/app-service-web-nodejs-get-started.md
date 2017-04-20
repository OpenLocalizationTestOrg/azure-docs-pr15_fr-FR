<properties
    pageTitle="Prise en main Node.js web apps dans Azure Application Service"
    description="Découvrez comment déployer une application Node.js sur une application web dans le Service d’application Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Prise en main Node.js web apps dans Azure Application Service

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel montre comment créer une application [Node.js] simple et déployer [Azure application] service à partir d’un environnement de ligne de commande, tels que cmd.exe ou bash. Vous pouvez suivre les instructions présentées dans ce didacticiel sur tous les systèmes d’exploitation qui peuvent être exécutées Node.js.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>Conditions préalables

- [Node.js]
- [Bower]
- [Yeoman]
- [GIT]
- [Azure infrastructure du langage commun]
- Un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire à une version d’évaluation gratuite] ou [activer vos avantages d’abonné Visual Studio].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Créer et déployer une application web Node.js simple

1. Ouvrez le terminal de ligne de commande de votre choix et installez [Express générateur pour Yeoman].

        npm install -g generator-express

2. `CD`dans un répertoire de travail et générer une application expresse à l’aide de la syntaxe suivante :

        yo express
        
    Choisir les options suivantes lorsque vous y êtes invité :  

    `? Would you like to create a new directory for your project?`**Oui**  
    `? Enter directory name`**{appname}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**Aucun**  
    `? Select a database to use:`**Aucun**  
    `? Select a build tool to use:`**Grunt**

3. `CD`dans le répertoire racine de votre nouvelle application et démarrer pour vous assurer qu’il est exécuté dans votre environnement de développement :

        npm start

    Dans votre navigateur, accédez à <http://localhost:3000> pour vous assurer que vous pouvez voir la page d’accueil Express. Une fois que vous avez vérifié votre application fonctionne correctement, utilisez `Ctrl-C` pour l’arrêter.
    
1. Passez en mode ASM et se connecter à Azure (vous devez [Azure infrastructure du langage commun](#prereq)) :

        azure config mode asm
        azure login

    Suivez l’invite pour continuer l’ouverture de session dans un navigateur avec un compte Microsoft qui comporte votre abonnement Azure.

2. Vérifiez que vous êtes toujours dans le répertoire racine de votre application, puis créez la ressource de l’application de Service d’application dans Azure avec un nom d’application unique avec la commande suivante. Par exemple : http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Suivez l’invite de sélection d’une région Azure à déployer. Si vous n’avez jamais configuré informations d’identification du déploiement Git/FTP pour votre abonnement Azure, vous êtes également invité les créer.

3. Ouvrez le fichier./config/config.js depuis la racine de votre application et modifiez le port de production pour `process.env.port`; votre `production` propriété dans la `config` objet doit ressembler à l’exemple suivant :

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Vous permet de votre application Node.js répondre pour web demandes sur le port par défaut qu’iisnode est à l’écoute.
    
4. Ouvrez./package.json et ajoutez la `engines` propriété pour [spécifier la version Node.js souhaitée](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Enregistrer vos modifications, puis utilisez git à déployer votre application Azure :

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Le Générateur de Express offre déjà un fichier .gitignore, de sorte que votre `git push` ne consommer la bande passante de la tentative de téléchargement de la node_modules / répertoire.

5. Pour finir, lancez votre application Azure live dans le navigateur :

        azure site browse

    Vous devez maintenant voir votre application web Node.js en direct dans le Service d’application Azure.
    
    ![Exemple de navigation à l’application déployée.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Mettre à jour votre application web Node.js

Pour apporter des mises à jour à votre application web Node.js en cours d’exécution dans le Service d’application, il suffit d’exécuter `git add`, `git commit`, et `git push` comme vous le faisiez lorsque vous avez déployé tout d’abord votre application web.
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>Comment application Service déploie votre application Node.js

Azure Application Service utilise [iisnode] pour exécuter les applications Node.js. L’infrastructure du langage commun Azure et le moteur Kudu (déploiement Git) travaillent ensemble pour vous fournir une expérience agréable lorsque vous développez et déployez des applications Node.js à partir de la ligne de commande. 

- `azure site create --git`reconnaît le modèle Node.js commun de server.js ou app.js et crée un iisnode.yml dans le répertoire racine. Vous pouvez utiliser ce fichier pour personnaliser iisnode.
- En `git push azure master`, Kudu automatiser les tâches de déploiement suivantes :

    - Si package.json se trouve dans la racine du référentiel, exécutez `npm install --production`.
    - Générer un fichier Web.config pour iisnode qui pointe vers votre script Démarrer dans package.json (par exemple, server.js ou app.js).
    - Personnaliser Web.config pour préparer votre application pour le débogage avec l’inspecteur de nœud.
    
## <a name="use-a-nodejs-framework"></a>Utiliser une infrastructure Node.js

Si vous utilisez un cadre Node.js courantes, telles que [Sails.js] [ SAILSJS] ou [MEAN.js] [ MEANJS] pour développer des applications, vous pouvez déployer ceux au Service d’application. Structures Node.js populaires ont leurs faiblesses spécifiques, et leurs dépendances package conservent mise à jour. Toutefois, application Service rend les journaux stdout et stderr disponibles, afin de pouvoir connaître exactement quoi de neuf avec votre application et apporter des modifications en conséquence. Pour plus d’informations, consultez [obtenir les journaux stdout et stderr à partir d’iisnode](#iisnodelog).

Les didacticiels suivants vous montrent comment travailler avec un cadre spécifique dans le Service d’application :

- [Déployer une application web Sails.js au Service d’application Azure]
- [Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure]
- [Comment utiliser io.js avec Azure Application Service Web Apps]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>Utiliser un moteur Node.js spécifique

Dans votre flux de travail par défaut, vous pouvez indiquer Service d’application à utiliser un moteur Node.js spécifique comme vous le faites habituellement dans package.json.
Par exemple :

    "engines": {
        "node": "6.6.0"
    }, 

Le moteur de déploiement Kudu détermine le moteur Node.js à utiliser dans l’ordre suivant :

- Examinons tout d’abord iisnode.yml pour voir si `nodeProcessCommandLine` est spécifié. Si Oui, puis l’utiliser.
- Ensuite, examinez package.json pour voir si `"node": "..."` spécifié dans le `engines` objet. Si Oui, puis l’utiliser.
- Par défaut, choisissez une version Node.js par défaut.

>[AZURE.NOTE] Il est recommandé de définir explicitement le moteur Node.js souhaité. La version Node.js par défaut modifiables, et vous obtiendrez des erreurs dans votre application web Azure, car la version Node.js par défaut n’est pas adaptée à votre application.

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Obtenir les journaux stdout et stderr à partir d’iisnode

Pour lire les journaux iisnode, procédez comme suit.

> [AZURE.NOTE] Après avoir effectué ces étapes, les fichiers journaux n’existent pas jusqu'à ce qu’une erreur se produit.

1. Ouvrez le fichier iisnode.yml qui fournit l’infrastructure du langage commun Azure.

2. Définir les deux paramètres suivants : 

        loggingEnabled: true
        logDirectory: iisnode
    
    Ensemble, ils disent iisnode dans le Service d’application pour remettre sa sortie stdout et stderror la D:\home\site\wwwroot\**iisnode** répertoire.

3. Enregistrer vos modifications, puis transmission vos modifications sur Azure avec les commandes Git suivantes :

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode est désormais configuré. La procédure suivante vous montrer comment accéder à ces fichiers journaux.
     
4. Dans votre navigateur, accédez à la console de débogage Kudu pour votre application, qui se trouve à :

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Cette URL est différente de l’URL de l’application web par l’ajout de «*.scm.*» le nom DNS. Si vous omettez cet ajout à l’URL, vous obtenez une erreur 404.

5. Accédez à D:\home\site\wwwroot\iisnode

    ![Naviguer vers l’emplacement des fichiers journaux iisnode.][iislog-kudu-console-find]

6. Cliquez sur l’icône **Modifier** pour le journal que vous souhaitez lire. Vous pouvez également cliquer sur **Télécharger** ou **Supprimer** si vous voulez.

    ![Ouvrez un fichier journal iisnode.][iislog-kudu-console-open]

    Vous pouvez maintenant voir le journal pour vous aider à déboguer votre déploiement de Service d’application.
    
    ![Examiner un fichier journal iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Déboguer votre application avec nœud inspecteur

Si vous utilisez inspecteur de nœud déboguer vos applications Node.js, vous pouvez l’utiliser pour votre application de Service d’application live. Inspecteur de nœud est préinstallé sur l’installation d’iisnode pour le Service d’application. Et si vous déployez via Git, Web.config généré à partir de Kudu contient déjà tous les la configuration, que vous devez activer l’inspecteur de nœud.

Pour activer l’inspecteur de nœud, procédez comme suit :

1. Ouvrez iisnode.yml à votre racine référentiel et spécifiez les paramètres suivants : 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Enregistrer vos modifications, puis transmission vos modifications sur Azure avec les commandes Git suivantes :

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. À présent, simplement accédez au fichier de démarrage de votre application comme spécifié par le script de démarrage dans votre package.json avec /debug ajouté à l’URL. Par exemple,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Ou,
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Ressources complémentaires

- [Spécifier une version Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md)
- [Meilleures pratiques et guide de dépannage pour les applications Node.js sur Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Le débogage d’une application web Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md)
- [Utilisation des Modules Node.js avec applications Azure](../nodejs-use-node-modules-azure-apps.md)
- [Azure Application Service Web applications : Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centre de développement Node.js](/develop/nodejs/)
- [Utiliser des applications web dans le Service d’application Azure](app-service-web-get-started.md)
- [Exploration de la Console de débogage confiant Super Kudu]

<!-- URL List -->

[Azure infrastructure du langage commun]: ../xplat-cli-install.md
[Service application Azure]: ../app-service/app-service-value-prop-what-is.md
[activer vos avantages d’abonné Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Créer une application de conversation Node.js avec Socket.IO dans le Service d’application Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Déployer une application web Sails.js au Service d’application Azure]: ./app-service-web-nodejs-sails.md
[Exploration de la Console de débogage confiant Super Kudu]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Générateur de Express pour Yeoman]: https://github.com/petecoop/generator-express
[GIT]: http://www.git-scm.com/downloads
[Comment utiliser io.js avec Azure Application Service Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[s’inscrire à une version d’évaluation gratuite]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
