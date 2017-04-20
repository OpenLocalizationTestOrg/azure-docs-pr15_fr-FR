<properties 
    pageTitle="Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix" 
    description="Didacticiel qui vous apprend à utiliser WebMatrix pour développer une application Node.js et déployez dans Azure Application Service Web Apps." 
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


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Créer et déployer une application web Node.js dans Azure à l’aide de WebMatrix

Ce didacticiel montre comment utiliser WebMatrix pour développer une application Node.js et déployez dans [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. WebMatrix est un outil de développement web gratuit de Microsoft qui inclut tout ce dont vous avez besoin pour le développement d’application web ou site Web. WebMatrix inclut plusieurs fonctionnalités qui le rendent Node.js facile à utiliser, y compris de saisie semi-automatique du code, des modèles préconçus et prise en charge de l’éditeur de Jade, moins et CoffeeScript. Apprenez-en davantage sur [WebMatrix](https://www.microsoft.com/web/webmatrix/).

À la fin de ce guide, vous avez une application web Node.js en cours d’exécution dans le Service d’application Azure.
 
Capture d’écran de l’application terminée est inférieur à :

![Site Web nœud Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="sign-into-azure"></a>Connectez-vous à Azure

Suivez ces étapes pour créer une application web dans le Service d’application Azure.

1. Barre de lancement WebMatrix
2. Si c’est la première fois que vous avez utilisé WebMatrix, vous serez invité à vous connecter à Azure.  Dans le cas contraire, vous pouvez cliquer sur le bouton **Se connecter** et sélectionnez **Ajouter un compte**.  Sélectionnez **se connecter** à l’aide de votre Account Microsoft.

    ![Ajouter un compte][addaccount]

3. Si vous êtes inscrit à un compte Azure, vous pourrez vous connecter à l’aide de votre Account Microsoft :

    ![Connectez-vous à Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Créer un site à l’aide d’un modèle intégré pour Azure

1. Dans l’écran d’accueil, cliquez sur le bouton **Nouveau** , puis choisissez **La galerie de modèles** pour créer un nouveau site à partir de la galerie de modèles :

    ![Nouveau site de galerie de modèles][sitefromtemplate]

2. Dans la boîte de dialogue **Site à partir du modèle** , sélectionnez **nœud** , puis sélectionnez **Site Express**. Pour finir, cliquez sur **suivant**. Si vous ne disposez pas des conditions requises pour le modèle de **Site Express** , vous devrez installez-les.

    ![Sélectionnez modèle express][webmatrix-templates]

3. Si vous êtes connecté à Azure, vous avez désormais la possibilité pour créer une application Service web app pour votre site local.  Choisissez un nom unique, puis sélectionnez le centre de données où vous voulez que votre application web application Service à créer : 

    ![Créer un site sur Azure][nodesitefromtemplateazure]
    
4. WebMatrix après construction le site local et la création du Service d’application web app, l’IDE WebMatrix s’affiche.

    ![WebMatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>Publier votre application sur Azure

1. Dans WebMatrix, cliquez sur **Publier** à partir du ruban **accueil** pour afficher la boîte de dialogue **Aperçu avant publication** pour le site.

    ![Aperçu de la publication][webmatrix-node-publishpreview]

2. Cliquez sur **Continuer**. Une fois la publication terminée, l’URL pour le Service d’application web app s’affiche en bas de l’IDE WebMatrix

    ![publication terminée][webmatrix-publish-complete]

3. Cliquez sur le lien pour ouvrir l’application web application Service dans votre navigateur.

    ![Application web Express][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>Modifier et republier votre application

Vous pouvez facilement modifier et republier votre application. Ici, vous effectuerez une simple modification sur le titre dans le fichier **index.jade** et republier l’application.

1. Dans WebMatrix, sélectionnez **les fichiers**et puis développez le dossier **affichages** . Ouvrez le fichier **index.jade** en double-cliquant dessus.

    ![WebMatrix l’affichage index.jade][webmatrix-modify-index]

2. Modifiez la ligne de paragraphe à ce qui suit :

        p Welcome to #{title} with WebMatrix on Azure!

3. Enregistrer vos modifications, puis cliquez sur l’icône publier. Pour finir, cliquez sur **Continuer** dans la boîte de dialogue **Aperçu avant publication** et attendez la mise à jour doit être publié.

    ![Aperçu de la publication][webmatrix-republish]

4. Une fois la publication terminée, cliquez sur le lien renvoyé une fois le processus de publication terminée pour afficher l’application web application Service mis à jour.

    ![Nœud Azure dans le navigateur][webmatrix-node-completed]

##<a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les versions de Node.js fournis avec Azure et comment spécifier la version à utiliser avec votre application, voir [spécification d’une version Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md).

Si vous rencontrez des problèmes avec votre application après que qu’il a été déployé sur Azure, voir [comment déboguer une application web Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md) pour plus d’informations sur le diagnostic du problème.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 