<properties
    pageTitle="Prise en main Azure Mobile Engagement pour les applications Web | Microsoft Azure"
    description="Découvrez comment utiliser Azure Mobile Engagement avec notifications analytique et push pour les applications Web."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Prise en main Azure Mobile Engagement pour les applications Web

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application Web.

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2015 ou tout autre éditeur de votre choix
+ [Kit de développement Web](http://aka.ms/P7b453) 

Ce kit de développement Web est en mode Aperçu avant uniquement prend en charge Analytique pour le moment et non prises en charge les notifications push envoi navigateur ou dans l’application encore. 

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##<a name="setup-mobile-engagement-for-your-web-app"></a>Le programme d’installation Mobile Engagement pour votre application Web

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration simple » qui correspond au jeu minimal requis pour recueillir des données.

Nous allons créer une application web de base avec Visual Studio pour illustrer l’intégration bien que vous pouvez suivre les étapes avec n’importe quelle application web également créée en dehors de Visual Studio. 

###<a name="create-a-new-web-app"></a>Créer une nouvelle application Web

Les étapes suivantes impliquent l’utilisation de Visual Studio 2015 bien que les étapes sont identiques dans les versions antérieures de Visual Studio. 

1. Démarrez Visual Studio, puis dans l’écran **d’accueil** , sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez **Web** -> **Application Web ASP.Net**. Dans l’application de **nom**, **emplacement** et le **nom de la Solution**, de remplissage, puis sur **OK**.

3. Dans le menu contextuel **Sélectionner un modèle** , sélectionnez **vide** sous **ASP.Net 4.5 modèles** , puis cliquez sur **OK**. 

Vous venez de créer un nouveau projet application Web vide dans lequel nous allez intégrer le SDK Azure Mobile Engagement Web.

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connecter votre application pour Mobile Engagement principal

1. Créer un nouveau dossier nommé **javascript** dans votre solution et ajouter fichier de Web SDK JS **azure engagement.js** dedans. 

2. Ajouter un nouveau fichier nommé **main.js** dans ce dossier javascript par le code suivant. Veillez à mettre à jour la chaîne de connexion. Cela `azureEngagement` objet sera utilisé pour accéder aux méthodes Web SDK. 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio avec fichiers js][1]

##<a name="enable-real-time-monitoring"></a>Activer le contrôle en temps réel

Avant de commencer l’envoi de données et de s’assurer que les utilisateurs sont actifs, vous devez envoyer au moins une activité sur le serveur principal Mobile Engagement. Une activité dans le contexte d’une application web est une page web. 

1. Créer une nouvelle page appelée **home.html** dans votre solution et le configurer comme la numérotation des pages de votre application web. 
2. Inclure les scripts deux JavaScript que nous avons ajouté précédemment dans cette page en ajoutant ce qui suit dans la balise de corps. 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Mettre à jour la balise de corps pour appeler de EngagementAgent `startActivity` méthode
        
        <body onload="engagement.agent.startActivity('Home')">

4. Voici à quoi ressemblera votre **home.html**
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>Étendre analytique

Voici toutes les méthodes actuellement disponible avec SDK Web que vous pouvez utiliser pour analytique :

1. Pages Web/activités :

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. Événements
        
        engagement.agent.sendEvent(name, extras);

3. Erreurs

        engagement.agent.sendError(name, extras);

4. Tâches

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

