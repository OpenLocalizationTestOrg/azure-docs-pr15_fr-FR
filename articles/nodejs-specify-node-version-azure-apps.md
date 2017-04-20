<properties
    pageTitle="Spécification d’une Version Node.js"
    description="Découvrez comment spécifier la version du Node.js utilisés par les Sites Web Azure et les Services en nuage"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Spécifier une version Node.js dans une application Azure

Lorsque vous hébergez une application Node.js, vous voudrez peut-être vous assurer que votre application utilise une version spécifique du Node.js. Il existe plusieurs méthodes pour effectuer cette opération pour les applications hébergées sur Azure.

##<a name="default-versions"></a>Versions par défaut

Les versions Node.js fournies par Azure sont à jour en permanence. Sauf indication contraire, la version par défaut spécifié dans le `WEBSITE_NODE_DEFAULT_VERSION` variable d’environnement sera utilisée. Pour ignorer cette valeur par défaut, suivez les étapes décrites dans les sections suivantes de cet article

> [AZURE.NOTE] Si vous hébergez votre application dans un Service de nuage Azure (rôle web ou de travail), et que la première fois que vous avez déployé l’application, Azure tentera d’utiliser la même version de Node.js que vous avez installé sur votre environnement de développement s’il correspond à une des versions par défaut disponibles sur Azure.

##<a name="versioning-with-packagejson"></a>Contrôle de version avec package.json

Vous pouvez spécifier la version de Node.js à utiliser en ajoutant les éléments suivants à votre fichier **package.json** :

    "engines":{"node":version}

Où *version* est le numéro de version spécifique à utiliser. Vous pouvez pouvez spécifier des conditions plus complexes pour la version, telles que :

    "engines":{"node": "0.6.22 || 0.8.x"}

Dans la mesure où 0.6.22 ne fait pas partie de la version disponible dans l’environnement d’hébergement, la version la plus récente de la 0,8 série disponible pourra être utilisé à la place - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Le contrôle de version des sites Web avec des paramètres de l’application
Si vous hébergez l’application d’un site Web, vous pouvez définir la variable d’environnement **WEBSITE_NODE_DEFAULT_VERSION** à la version souhaitée. 

##<a name="versioning-cloud-services-with-powershell"></a>Services de Cloud le contrôle de version avec PowerShell

Si vous hébergez l’application dans un Service Cloud et déployez de l’application à l’aide de PowerShell Azure, vous pouvez ignorer la version Node.js par défaut à l’aide de l’applet de commande PowerShell **Set-AzureServiceProjectRole** . Par exemple :

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Notez que les paramètres de l’instruction ci-dessus respectent la casse.  Vous pouvez vérifier que la version correcte de Node.js a été sélectionnée en vérifiant la propriété **moteurs** dans **package.json votre rôle**.

Vous pouvez également utiliser **Get-AzureServiceProjectRoleRuntime** pour récupérer la liste des versions Node.js disponibles pour les applications hébergées comme un Service Cloud.  Toujours vérifier la version de Node.js dépend de votre projet est dans cette liste.

##<a name="using-a-custom-version-with-azure-websites"></a>À l’aide d’une version personnalisée avec des sites Web Azure

Alors que Azure fournit plusieurs versions par défaut de Node.js, vous souhaiterez peut-être utiliser une version qui n’est pas fournie par défaut. Si votre application est hébergée comme un site Web Azure, vous pouvez le faire en utilisant le fichier **iisnode.yml** . Les étapes suivantes décrivent le processus de l’utilisation d’une version personnalisée de Node.Js avec un site Web Azure :

1. Créer un nouveau répertoire, puis créez un fichier **server.js** dans l’annuaire. Le fichier **server.js** doit contenir les éléments suivants :

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    La version Node.js utilisée lorsque vous naviguez sur le site Web s’affiche.

2. Créer un nouveau site Web et notez le nom du site. Par exemple, les éléments suivants utilisant les [Outils de ligne de commande Azure] pour créer un nouveau site Web Azure nommé **monsiteweb**et activer un référentiel Git pour le site Web.

        azure site create mywebsite --git

3. Créer un nouveau répertoire nommé **bin** comme enfant du répertoire contenant le fichier **server.js** .

4. Télécharger la version spécifique de **node.exe** (la version de Windows) que vous souhaitez utiliser avec votre application. Par exemple, l’exemple suivant utilise **coin** pour télécharger la version 0.8.1 :

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Enregistrez le fichier **node.exe** dans le dossier **bin** créé précédemment.

5. Créer un fichier **iisnode.yml** dans le même répertoire que le fichier **server.js** et ajoutez le contenu suivant dans le fichier **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Ce chemin d’accès est l’endroit où le fichier **node.exe** au sein de votre projet se trouve une fois que vous avez publié votre application pour le site Web Azure.

6. Publiez votre application. Par exemple, étant donné que j’ai créé un nouveau site Web avec le paramètre--git précédemment, les commandes suivantes ajouter les fichiers d’application à mon référentiel Git local et puis les dirige vers le référentiel de site Web :

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Une fois que l’application a publié, ouvrez le site Web dans un navigateur. Vous devez voir un message indiquant « Bonjour à partir d’Azure version nœud en cours d’exécution : v0.8.1 ».

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment spécifier la version du Node.js utilisée par votre application, découvrez comment [utiliser des modules], [créer et déployer un Site Web Node.js]et [comment utiliser les outils de ligne de commande Azure pour Mac et Linux].

Pour plus d’informations, voir le [Centre de développement Node.js](/develop/nodejs/).

[Comment utiliser les outils de ligne de commande Azure pour Mac et Linux]: xplat-cli-install.md
[Outils de ligne de commande Azure]: xplat-cli-install.md
[utiliser des modules]: nodejs-use-node-modules-azure-apps.md
[créer et déployer un Site Web Node.js]: web-sites-nodejs-develop-deploy-mac.md
