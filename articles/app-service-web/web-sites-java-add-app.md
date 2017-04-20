<properties 
    pageTitle="Ajouter une application Java Azure Application Service Web Apps" 
    description="Ce didacticiel montre comment ajouter une page ou une application à votre instance d’Azure Application Service Web Apps est déjà configuré pour utiliser Java." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Ajouter une application Java Azure Application Service Web Apps

Une fois que vous avez initialisé votre application web Java dans le [Service d’application Azure][] comme indiqué à [créer une application web Java dans le Service d’application Azure](web-sites-java-get-started.md), vous pouvez télécharger votre application en plaçant vos guerre dans le dossier **applications Web** .

Le chemin d’accès de navigation vers le dossier **applications Web** varie selon la configuration d’instance de vos applications Web.

- Si vous configurer votre application web à l’aide de la Azure Marketplace, le chemin d’accès au dossier **WebApp** est dans l’écran **d:\home\site\wwwroot\bin\application\_server\webapps**, où **application\_serveur** est le nom du serveur d’applications en vigueur pour l’instance du Web Apps. 
- Si vous configurer votre application web à l’aide de l’interface utilisateur de configuration Azure, le chemin d’accès au dossier **WebApp** est dans le formulaire **d:\home\site\wwwroot\webapps**. 

Notez que vous pouvez utiliser le contrôle de code source pour télécharger votre application ou des pages web, y compris des [scénarios d’intégration continue](app-service-continuous-deployment.md). FTP est également une option pour le téléchargement de votre application ou des pages web.

Remarque pour les applications web Tomcat : une fois que vous avez téléchargé votre fichier WAR vers le dossier **applications Web** , le serveur d’applications Tomcat détecte que vous avez ajouté et chargez automatiquement. Remarque : que si vous copiez des fichiers (autres que les fichiers WAR) dans le répertoire racine, le serveur d’application à être redémarré pour que ces fichiers sont utilisés. La fonctionnalité autoload pour les applications web Java Tomcat s’exécutant sur Azure est basée sur un nouveau fichier WAR ajouté, ou des fichiers ou répertoires ajoutés dans le dossier **applications Web** . 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Service application Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
