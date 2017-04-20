<properties
    pageTitle="Node.js mise en route | Microsoft Azure"
    description="Découvrez comment créer une application web Node.js simple et déployer auprès d’un service cloud Azure."
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
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Créer et déployer une application Node.js auprès d’un Service Cloud Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Ce didacticiel montre comment créer une application Node.js simple en cours d’exécution dans un Service de nuage Azure. Services en nuage sont des blocs de construction d’applications cloud scalable dans Azure. Ils permettent de séparation et gestion indépendante et horizontale de composants principaux et frontaux de votre application.  Les Services en nuage fournissent une machine virtuelle dédiée robuste pour chaque rôle d’hébergement fiable.

Pour plus d’informations sur les Services en nuage, et comment ils comparent aux sites Web Azure et machines virtuelles, voir [Azure sites Web, Services Cloud et comparaison de Machines virtuelles].

>[AZURE.TIP] Vous aimeriez pour créer un site Web simple ? Si votre scénario implique simplement un simple site Web frontaux, envisagez [d’utiliser une application web légère]. Vous pouvez facilement mettre à niveau vers un Service Cloud que votre application web se développe et l’évolution de vos besoins.

En suivant ce didacticiel, vous allez créer une application web simple hébergée dans un rôle web. Vous allez utiliser l’émulateur cluster pour tester votre application localement, puis déployer à l’aide des outils de ligne de commande PowerShell.

L’application est une application simple « Bonjour » :

![Un navigateur web affichage de la page web Bonjour][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Conditions préalables

> [AZURE.NOTE] Ce didacticiel utilise Azure PowerShell, ce qui requiert Windows.

- Installez et configurez [Azure Powershell].
- Téléchargez et installez le [Kit de développement Azure pour .NET 2.7]. Dans la configuration d’installation, sélectionnez :
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>Créer un projet de Service Cloud Azure

Effectuer les tâches suivantes pour créer un nouveau projet Azure Cloud Service, ainsi que la structure Node.js base :

1. Exécuter **Windows PowerShell** en tant qu’administrateur ; dans le **Menu Démarrer** ou **l’Écran de démarrage**, recherchez les **Windows PowerShell**.

2. [PowerShell se connecter] à votre abonnement.

3. Entrez l’applet de commande PowerShell suivante pour créer pour créer le projet :

        New-AzureServiceProject helloworld

    ![Le résultat de la commande helloworld nouveau AzureService][The result of the New-AzureService helloworld command]

    L’applet de commande **New-AzureServiceProject** génère une structure de base pour la publication d’une application Node.js sur un Service Cloud. Il contient des fichiers de configuration nécessaires pour la publication sur Azure. L’applet de commande change également votre répertoire de travail à l’annuaire pour le service.

    L’applet de commande crée les fichiers suivants :

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** et **ServiceDefinition.csdef**: Azure spécifiques fichiers nécessaires pour la publication de votre application. Pour plus d’informations, voir [vue d’ensemble de la création d’un Service hébergé pour Azure].

    -   **deploymentSettings.json**: stocke les paramètres locaux sont utilisés par les applets de commande PowerShell Azure déploiement.

4.  Entrez la commande suivante pour ajouter un nouveau rôle web :

        Add-AzureNodeWebRole

    ![Le résultat de la commande Ajouter AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    L’applet de commande **Add-AzureNodeWebRole** crée une application Node.js base. Il modifie également les fichiers **.csfg** et **.csdef** pour ajouter des entrées de configuration pour le nouveau rôle.

    > [AZURE.NOTE] Si vous ne spécifiez pas un nom de rôle, un nom par défaut est utilisé. Vous pouvez fournir un nom comme premier paramètre applet de commande :`Add-AzureNodeWebRole MyRole`

L’application Node.js est définie dans le fichier **server.js**, situé dans le répertoire pour le rôle web (**WebRole1** par défaut). Voici le code :

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Ce code est pour l’essentiel identique à l’exemple « Hello World » sur le site Web [nodejs.org] , sauf qu’elle utilise le numéro de port affecté par l’environnement cloud.

## <a name="deploy-the-application-to-azure"></a>Déploiement de l’application vers Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>Télécharger les paramètres de publication Azure

Pour déployer votre application dans Azure, vous devez d’abord télécharger les paramètres de publication pour votre abonnement Azure.

1.  Exécutez l’applet de commande PowerShell Azure suivant :

        Get-AzurePublishSettingsFile

    Utiliser votre navigateur pour accéder à la page de téléchargement des paramètres de publication. Vous pouvez être invité à vous connecter avec un Account Microsoft. Si c’est le cas, utilisez le compte associé à votre abonnement Azure.

    Enregistrez le profil téléchargé vers un emplacement de fichier que vous pouvez facilement accéder.

2.  Exécutez la suite applet de commande pour importer le profil de publication que vous avez téléchargé :

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Après avoir importé les paramètres de publication, vous pouvez supprimer le fichier téléchargé .publishSettings, car elle contient des informations qui peuvent permettre à un utilisateur pour accéder à votre compte.

### <a name="publish-the-application"></a>Publication de l’application

Pour publier, exécutez les commandes suivantes :

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** indique le nom pour le déploiement. Cela doit être un nom unique, sinon le processus de publication échouera. La commande **Get-Date** s’attache à une chaîne de date/heure qui doit rendre le nom unique.

- **-Emplacement** Spécifie le centre de données qui sera hébergée dans l’application. Pour afficher la liste des centres de données disponibles, utilisez l’applet de commande **Get-AzureLocation** .

- **-Lancement** s’ouvre une fenêtre de navigateur et affiche le service hébergé issue de déploiement.

Une fois la publication établie, vous verrez une réponse similaire à ce qui suit :

![Le résultat de la commande Publier AzureService][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> Il peut prendre plusieurs minutes pour l’application à déployer et deviennent disponibles lors de la première publication.

Une fois le déploiement terminé, une fenêtre de navigateur s’ouvrir et accédez au service cloud.

![Une fenêtre de navigateur affichant la page du monde Bonjour ; l’URL indique la page est hébergée sur Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Votre application s’exécute désormais sur Azure.

L’applet de commande **Publier AzureServiceProject** effectue les opérations suivantes :

1.  Crée un package de déploiement. Le package contient tous les fichiers dans votre dossier d’application.

2.  Crée un nouveau **compte de stockage** s’il n’existe pas. Le compte de stockage Azure permet de stocker le package d’application pendant le déploiement. Vous pouvez supprimer en toute sécurité du compte de stockage après le déploiement.

3.  Crée un **service cloud** s’il n’existe pas déjà. Un **service cloud** est le conteneur dans lequel votre application est hébergée lorsqu’elle est déployée vers Azure. Pour plus d’informations, voir [vue d’ensemble de la création d’un Service hébergé pour Azure].

4.  Publie le package de déploiement sur Azure.


## <a name="stopping-and-deleting-your-application"></a>Arrêt et suppression de votre application

Après le déploiement de votre application, vous souhaiterez peut-être désactivez-le afin d’éviter les frais supplémentaires. Factures Azure web instances de rôle par heure du serveur temps écoulé. Heure du serveur est utilisée une fois que votre application est déployée, même si les instances ne sont pas en cours d’exécution et à l’état arrêté.

1.  Dans la fenêtre de Windows PowerShell, arrêtez le déploiement de service créé dans la section précédente avec l’applet de commande suivante :

        Stop-AzureService

    Arrêt du service peut prendre plusieurs minutes. Lorsque le service est arrêté, vous recevez un message indiquant que le programme a cessé.

    ![L’état de la commande Arrêter AzureService][The status of the Stop-AzureService command]

2.  Pour supprimer le service, appelez l’applet de commande suivante :

        Remove-AzureService

    Lorsque vous y êtes invité, saisissez **Y** pour supprimer le service.

    Suppression du service peut prendre plusieurs minutes. Une fois que le service a été supprimé vous recevez un message indiquant que le service a été supprimé.

    ![L’état de la commande Supprimer AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] Suppression du service ne supprime pas le compte de stockage qui a été créé lorsque le service a été publié à l’origine, puis vous continuent à être facturé pour le stockage utilisé. Si rien d’autre utilise le stockage, vous souhaiterez peut-être la supprimer.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement Node.js].

<!-- URL List -->

[Comparaison de sites Web, Services Cloud et Machines virtuelles Azure]: ../app-service-web/choose-web-site-cloud-service-vm.md
[à l’aide d’une application web légère]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Powershell Azure]: ../powershell-install-configure.md
[Kit de développement Azure pour .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Se connecter PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Présentation de la création d’un Service hébergé pour Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Centre de développement Node.js]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
