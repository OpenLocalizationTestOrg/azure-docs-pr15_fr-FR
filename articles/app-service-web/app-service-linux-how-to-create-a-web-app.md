<properties 
    pageTitle="Comment créer une application Web avec le Service d’application sur Linux | Microsoft Azure" 
    description="Web application création flux de travail pour le Service d’application sur Linux." 
    keywords="service d’application Azure, dans le navigateur, linux, systèmes d’exploitation"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="create-a-web-app-with-app-service-on-linux"></a>Créer une application Web avec le Service d’application sous Linux

## <a name="using-the-management-portal-to-create-your-web-app"></a>À l’aide du portail de gestion pour créer votre application web
Vous pouvez commencer la création de votre application Web sous Linux à partir du [portail de gestion](https://portal.azure.com) comme le montre l’image ci-dessous.

![][1]

Une fois que vous sélectionnez l’option ci-dessous, vous verrez la carte créer comme le montre l’image ci-dessous. 

![][2]

-   Donnez un nom à votre application web.
-   Choisissez un groupe de ressources existant ou créez-en un. (Voir régions disponibles dans la [section limitations](./app-service-linux-intro.md)).
-   Choisissez un plan de service d’application existant ou créez-en un nouveau une (voir application service plan Remarques dans la [section limitations](./app-service-linux-intro.md)). 
-   Choisissez la pile d’applications que vous souhaitez utiliser. Vous obtenez le choix entre plusieurs versions de Node.js et PHP. 

Une fois que l’application créée, vous pouvez modifier la pile d’applications à partir des paramètres de l’application, comme le montre l’image ci-dessous.

![][3]

## <a name="deploying-your-web-app"></a>Déploiement de votre application web

Choisir « options de déploiement » à partir du portail de gestion vous donne la possibilité d’utiliser local un référentiel Git ou un référentiel GitHub pour déployer votre application. Les instructions sont par la suite de la même façon pour une application web non-Linux et vous pouvez suivre les instructions dans notre [déploiement Git local](./app-service-deploy-local-git.md) ou notre article [déploiement continue](./app-service-continuous-deployment.md) pour GitHub.

Vous pouvez également utiliser FTP pour télécharger votre application sur votre site. Vous pouvez obtenir le point de terminaison FTP pour votre application web à partir de la section journaux de diagnostics comme le montre l’image ci-dessous.

![][4]


## <a name="next-steps"></a>Étapes suivantes ##

* [Quel est le Service d’application sur Linux ?](./app-service-linux-intro.md)
* [À l’aide de la Configuration de PM2 pour Node.js dans les applications Web sous Linux](./app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png
