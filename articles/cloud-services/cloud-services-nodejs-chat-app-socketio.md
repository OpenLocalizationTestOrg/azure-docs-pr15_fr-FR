<properties 
    pageTitle="Application Node.js à l’aide de Socket.io | Microsoft Azure" 
    description="Découvrez comment utiliser socket.io dans une application node.js hébergée sur Azure." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Créer une Application Node.js conversation avec Socket.IO sur un Service Cloud Azure

Socket.IO fournit la communication en temps réel entre vos clients et serveur node.js. Ce didacticiel vous guidera un socket d’hébergement. IO en fonction d’application de conversation sur Azure. Pour plus d’informations sur Socket.IO, voir <http://socket.io/>.

Capture d’écran de l’application terminée est inférieur à :

![Une fenêtre de navigateur affichant le service hébergé sur Azure][completed-app]  

## <a name="prerequisites"></a>Conditions préalables

Assurez-vous que les produits et les versions suivants sont installées pour terminer l’exemple dans cet article :

* Installer [Visual Studio 2013](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Installer [Node.js](https://nodejs.org/download/)
* Installer [Python version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Créer un projet de Service Cloud

Les étapes suivantes créent le projet de service cloud qui héberge l’application Socket.IO.

1. Dans le **Menu Démarrer** ou **l’Écran de démarrage**, recherchez les **Windows PowerShell**. Enfin, avec le bouton droit de **Windows PowerShell** et sélectionnez **Exécuter en tant qu’administrateur**.

    ![Icône PowerShell Azure][powershell-menu]

2. Créez un répertoire appelé **c:\\nœud**. 
 
        PS C:\> md node

3. Accédez à la **c :\\nœud** répertoire
 
        PS C:\> cd node

4. Entrez les commandes suivantes pour créer une solution nommé **chatapp** et un rôle de collaborateur nommé **WorkerRole1**:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Vous verrez la réponse suivante :

    ![Le résultat de la nouvelle azureservice et azurenodeworkerrolecmdlets ajouter](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Télécharger l’exemple de conversation

Pour ce projet, nous allons utiliser l’exemple de conversation à partir du [référentiel Socket.IO GitHub]. Effectuez les opérations suivantes pour télécharger l’exemple et ajoutez au projet que vous avez créé précédemment.

1.  Créer une copie locale du référentiel en utilisant le bouton **Dupliquer** . Vous pouvez également utiliser le bouton **ZIP** pour télécharger le projet.

    ![Une fenêtre de navigateur affichage https://github.com/LearnBoost/socket.io/tree/master/examples/chat, avec l’icône de téléchargement ZIP mis en surbrillance][chat-example-view]

3.  Naviguez l’arborescence du référentiel local jusqu'à ce que vous atteindrez la **exemples\\conversation** répertoire. Copier le contenu de ce répertoire à la **c :\\nœud\\chatapp\\WorkerRole1** répertoire créé précédemment.

    ![Explorer, afficher le contenu des exemples\\répertoire conversation extrait depuis l’archive ;][chat-contents]

    Les éléments en surbrillance dans la capture d’écran ci-dessus sont les fichiers copiées à partir de la **exemples\\conversation** répertoire

4.  Dans la **C:\\nœud\\chatapp\\WorkerRole1** répertoire, supprimez le fichier **server.js** et puis renommez le fichier **app.js** en **server.js**. Cette action supprime le fichier de **server.js** par défaut créé précédemment par l’applet de commande **Add-AzureNodeWorkerRole** et remplace par le fichier d’application à partir de l’exemple de conversation.

### <a name="modify-serverjs-and-install-modules"></a>Modifier Server.js et installer des Modules

Avant de tester l’application dans l’émulateur Azure, nous devons apporter de légères modifications. Effectuez les opérations suivantes dans le fichier server.js :

1.  Ouvrez le fichier **server.js** dans Visual Studio ou un éditeur de texte.

2.  Recherchez la section de **dépendances de Module** au début du server.js et modifiez la ligne contenant **sio = require('.. //.. lib//Socket.IO')** à **sio = require('socket.io')** comme indiqué ci-dessous :

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Pour vous assurer que l’application écoute sur le port correct, ouvrez server.js dans le bloc-notes ou votre éditeur favori et puis modifiez la ligne suivante en remplaçant **3000** avec **process.env.port** comme indiqué ci-dessous :

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Après avoir enregistré les modifications apportées à **server.js**, procédez comme suit pour installer les modules requis et testez ensuite l’application dans l’émulateur Azure :

1.  À l’aide de **PowerShell Azure**, accédez à la **c :\\nœud\\chatapp\\WorkerRole1** répertoire et utilisez la commande suivante pour installer les modules requis par cette application :

        PS C:\node\chatapp\WorkerRole1> npm install

    Les modules répertoriés dans le fichier package.json seront installés. Une fois la commande terminée, vous devriez voir sortie similaire à ce qui suit :

    ![Commande d’installation de la sortie de la npm][The-output-of-the-npm-install-command]

4.  Étant donné que cet exemple trouvait, une partie du référentiel Socket.IO GitHub et directement référencées la bibliothèque Socket.IO par chemin d’accès relatif, Socket.IO n’était pas référencée dans le fichier package.json, afin que nous devons l’installer à l’aide de la commande suivante :

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Tester et déployer

1.  Lancez l’émulateur à l’aide de la commande suivante :

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Ouvrez un navigateur et accédez à **http://127.0.0.1**.

3.  Lorsque la fenêtre du navigateur s’ouvre, entrez un surnom et puis appuyez sur ENTRÉE.
    Cela sera tout vous permettent de publier des messages comme un surnom spécifique. Pour tester les fonctionnalités de plusieurs utilisateurs, ouvrez les fenêtres du navigateur supplémentaires à l’aide de la même URL et entrez plusieurs surnoms.

    ![Afficher les messages de conversation de l’utilisateur 1 et l’utilisateur 2 deux fenêtres du navigateur](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Après le test de l’application, arrêtez l’émulateur en exécutant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Pour déployer l’application vers Azure, utilisez l’applet de commande **Publier AzureServiceProject** . Par exemple :

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    > [AZURE.IMPORTANT] Veillez à utiliser un nom unique, sinon le processus de publication échouera. Une fois le déploiement terminé, le navigateur ouvrir et accédez au service déployé.
    > 
    > Si vous recevez un message d’erreur indiquant que le nom d’abonnement fourni n’existe pas dans le profil de publication importées, vous devez télécharger et importer le profil de publication pour votre abonnement avant de déployer sur Azure. Consultez la section **déploiement de l’Application vers Azure** de [créer et déployer une application Node.js auprès d’un Service Cloud Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

    ![Une fenêtre de navigateur affichant le service hébergé sur Azure][completed-app]

    > [AZURE.NOTE] Si vous recevez un message d’erreur indiquant que le nom d’abonnement fourni n’existe pas dans le profil de publication importées, vous devez télécharger et importer le profil de publication pour votre abonnement avant de déployer sur Azure. Consultez la section **déploiement de l’Application vers Azure** de [créer et déployer une application Node.js auprès d’un Service Cloud Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

Votre application est en cours d’exécution sur Azure et peut transmettre des messages de conversation entre différents clients à l’aide de Socket.IO.

> [AZURE.NOTE] Pour simplifier, cet exemple est limité aux discuter entre utilisateurs connectés à la même instance. Cela signifie que si le service cloud crée deux instances de rôle de collaborateur, les utilisateurs seulement pourront discuter avec d’autres personnes connecté à la même instance de rôle de collaborateur. Pour réduire l’application pour l’utiliser avec plusieurs instances de rôle, vous pouvez utiliser une technologie comme Bus des services pour partager l’état du magasin Socket.IO pour plusieurs instances. Pour obtenir des exemples, voir les exemples de l’utilisation de files d’attente de Bus de Service et les rubriques dans le [Kit de développement logiciel Azure pour Node.js GitHub référentiel](https://github.com/WindowsAzure/azure-sdk-for-node).

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment créer une application de conversation base hébergée dans un Service Cloud Azure. Pour savoir comment héberger cette application dans un site Web Azure, voir [créer une Application de conversation Node.js avec Socket.IO sur un Site Web de Azure][chatwebsite].

Pour plus d’informations, voir également le [Centre de développement Node.js](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Référentiel Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 
