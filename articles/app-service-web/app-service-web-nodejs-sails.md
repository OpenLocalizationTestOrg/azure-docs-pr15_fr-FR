<properties
    pageTitle="Déployer une application web Sails.js au Service d’application Azure"
    description="Découvrez comment déployer une application Node.js Azure Application Service. Ce didacticiel montre comment déployer une application web Sails.js."
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
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="cephalin"/>

# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Déployer une application web Sails.js au Service d’application Azure

Ce didacticiel montre comment déployer une application Sails.js au Service d’application Azure. Dans le processus, vous pouvez en déduire des connaissances générales sur la configuration de votre application Node.js à exécuter dans le Service d’application. 

Vous devez disposer des connaissances pratiques de Sails.js. Ce didacticiel n’est pas destiné à résoudre les problèmes liés à l’exécution Sail.js en général.


## <a name="prerequisites"></a>Conditions préalables

- [Node.js](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [GIT](http://www.git-scm.com/downloads)
- [Azure infrastructure du langage commun](../xplat-cli-install.md)
- Un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire à une version d’évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer vos avantages d’abonné Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Pour voir Service d’application Azure en action avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pouvez créer une application starter courte immédiatement dans le Service d’application : aucune carte de crédit requises, aucune engagements.

## <a name="step-1-create-a-sailsjs-app-locally"></a>Étape 1 : Créer une application Sails.js localement

Tout d’abord, rapidement créer une application de Sails.js par défaut dans votre environnement de développement en procédant comme suit :

1. Ouvrez le terminal de ligne de commande de votre choix et `CD` dans un répertoire de travail.

2. Créer une application Sails.js et l’exécuter :

        sails new <appname>
        cd <appname>
        sails lift

    Vérifiez que vous pouvez accéder à la page d’accueil par défaut en http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Étape 2 : Créer la ressource application Azure

Ensuite, créez la ressource de Service d’application dans Azure. Vous allez déployer votre application Sails.js lui ultérieurement.

1. se connecter à j’aime Azure ainsi :
1. Dans le même terminal, changer en mode ASM et connectez-vous à Azure :

        azure config mode asm
        azure login

    Suivez l’invite pour continuer l’ouverture de session dans un navigateur avec un compte Microsoft qui comporte votre abonnement Azure.

2. Vérifiez que vous êtes toujours dans le répertoire racine de votre projet Sails.js. Créer la ressource de l’application de Service d’application dans Azure avec un nom d’application unique avec la commande suivante. URL de votre application web est http://&lt;appname >. azurewebsites.net.

        azure site create --git <appname>

    Suivez l’invite de sélection d’une région Azure à déployer. Si vous n’avez jamais configuré informations d’identification du déploiement Git/FTP pour votre abonnement Azure, vous êtes également invité les créer.

    Une fois créée la ressource de l’application de Service d’application :

    - Application Sails.js est initialisé Git,
    - Votre référentiel initialisé Git local est connecté à la nouvelle application de Service d’application comme un Git à distance, bien nommé « azure », et
    - Et iisnode.yml fichier est créé dans le répertoire racine. Vous pouvez utiliser ce fichier pour configurer [iisnode](https://github.com/tjanczuk/iisnode)qui application Service utilise pour exécuter des applications Node.js.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Étape 3 : Configurer et déployer votre application Sails.js

 Travailler avec une application Sails.js dans le Service d’application se compose de trois étapes principales :

 - Configurer votre application pour qu’elle à exécuter dans le Service d’application
 - Déployer au Service d’application
 - Fichiers journaux stderr et stdout lecture à résoudre les éventuels problèmes de déploiement

Procédez comme suit :

1. Ouvrez le nouveau fichier iisnode.yml dans le répertoire racine et ajoutez les deux lignes suivantes :

        loggingEnabled: true
        logDirectory: iisnode

    Journalisation est désormais activée pour iisnode. Pour plus d’informations sur le fonctionnement, consultez  [obtenir les journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Ouvrez config/env/production.js pour configurer votre environnement de production et définir `port` et `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Vous pouvez trouver la documentation pour ces paramètres de configuration dans la  [Documentation Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Ensuite, vous devez vous assurer que [Grunt](https://www.npmjs.com/package/grunt) est compatible avec les lecteurs de réseau de Azure. Versions de Grunt inférieur à 1.0.0 utilise un package obsolètes [glob](https://www.npmjs.com/package/glob) (moins 5.0.14), qui ne prend pas en charge les lecteurs réseau. 

3. Ouvrez package.json et modifiez le `grunt` version à `1.0.0` et supprimer tous les `grunt-*` packages. Votre `dependencies` propriété doit ressembler à ceci :

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. Dans package.json, ajoutez le code suivant `engines` propriété pour définir la version Node.js pour qu’il nous voulons.

        "engines": {
            "node": "6.6.0"
        },

6. Enregistrez vos modifications et tester vos modifications pour vous assurer que votre application s’exécute toujours localement. Pour ce faire, supprimez le `node_modules` dossier et puis exécutez :

        npm install
        sails lift

4. À présent, utilisez git pour déployer votre application sur Azure :

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Pour finir, lancez votre application Azure live dans le navigateur :

        azure site browse

    Vous devez maintenant voir la page d’accueil Sails.js même.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Résoudre les problèmes de votre déploiement

Si votre application Sails.js échoue pour une raison quelconque dans le Service d’application, recherchez les journaux stderr pour aider à résoudre il.
Pour plus d’informations, consultez [obtenir les journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Si elle a démarré, le journal stdout doit afficher le message familier :

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Vous pouvez contrôler la précision des journaux stdout dans le fichier [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Se connecter à une base de données Azure

Pour vous connecter à une base de données Azure, vous créez la base de données de votre choix dans Azure, telles que la base de données SQL Azure, MySQL, MongoDB, Cache Azure (Redis), etc. et utilisez [carte de magasin de données](https://github.com/balderdashy/sails#compatibility) correspondant à s’y connecter. Les étapes décrites dans cette section vous montrent comment se connecter à une base de données MySQL dans Azure.

1. Suivez le didacticiel [ici](../store-php-create-mysql-database.md) pour créer une base de données MySQL dans Azure.

2. À partir de votre terminal de ligne de commande, installez la carte MySQL :

        npm install sails-mysql --save

3. Ouvrez config/connections.js et ajoutez l’objet suivant de la connexion à la liste : 

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. Pour chaque variable d’environnement (`process.env.*`), vous devez définir dans le Service d’application. Pour ce faire, exécutez les commandes suivantes à partir de votre terminal. Toutes les informations de connexion nécessaires se trouvent dans le portail Azure (voir [se connecter à votre base de données MySQL](../store-php-create-mysql-database.md#connect)).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    Placer vos paramètres dans les paramètres de l’application Azure conserve les données sensibles se déconnecter de votre contrôle de code source (Git). Ensuite, vous allez configurer votre environnement de développement pour utiliser les mêmes informations de connexion.

4. Ouvrez config/local.js et ajoutez l’objet de connexions suivants :

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Cette configuration remplace les paramètres dans votre fichier config/connections.js pour l’environnement local. Ce fichier est exclu par le .gitignore par défaut dans votre projet, afin qu’il ne sera pas stocké dans Git. À présent, vous êtes en mesure de vous connecter à votre base de données MySQL à partir de votre application web Azure et à partir de votre environnement de développement local.

4. Ouvrez config/env/production.js pour configurer votre environnement de production, puis ajoutez le code suivant `models` objet :

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Ouvrez config/env/development.js pour configurer votre environnement de développement et ajoutez le code suivant `models` objet :

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    `migrate: 'alter'`vous permet d’utiliser des fonctionnalités de migration de base de données pour créer et mettre à jour vos tables de base de données dans votre MySQL facilement. Toutefois, `migrate: 'safe'` est utilisé pour votre environnement Azure (production) car Sails.js ne vous permet pas d’utiliser `migrate: 'alter'` dans un environnement de production (voir la  [Documentation de Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. Dans le terminal, [Générer](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) un Sails.js [API plan](http://sailsjs.org/documentation/concepts/blueprints) comme vous normalement, puis exécuterez `sails lift` pour créer la base de données avec la migration de base de données Sails.js. Par exemple :

         sails generate api mywidget
         sails lift

    La `mywidget` modèle généré par cette commande est vide, mais nous pouvons l’utiliser pour afficher que nous avons connectivité de base de données.
    Lorsque vous exécutez `sails lift`, qu’il crée les tables manquantes pour les modèles de votre application utilise.

6. Accéder à l’API que vous venez de créer dans le navigateur de plan. Par exemple :

        http://localhost:1337/mywidget/create
    
    L’API doit renvoyer l’entrée créée avec vous dans la fenêtre de navigateur, ce qui signifie que votre base de données est créé avec succès.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. À présent, vos modifications de transmission pour Azure et accédez à votre application pour vérifier qu’il fonctionne toujours.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Accéder à l’API de plan de votre application web Azure. Par exemple :

        http://<appname>.azurewebsites.net/mywidget/create

    Si l’API renvoie un autre nouvelle entrée, votre application web Azure parle à votre base de données MySQL.

## <a name="more-resources"></a>Ressources complémentaires

- [Prise en main Node.js web apps dans Azure Application Service](app-service-web-nodejs-get-started.md)
- [Utilisation des Modules Node.js avec applications Azure](../nodejs-use-node-modules-azure-apps.md)
