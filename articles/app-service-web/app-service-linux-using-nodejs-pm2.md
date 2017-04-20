<properties 
    pageTitle="À l’aide de la Configuration de PM2 pour NodeJS dans les applications Web sur Linux | Microsoft Azure" 
    description="À l’aide de la Configuration de PM2 pour NodeJS dans les applications Web sous Linux" 
    keywords="service d’application Azure, dans le navigateur, nodejs, pm2, linux, systèmes d’exploitation"
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

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>À l’aide de la Configuration de PM2 pour Node.js dans les applications Web sous Linux

Si vous affectez la pile d’applications Node.js pour les applications Web sous Linux, vous obtiendrez l’option pour définir un fichier de démarrage Node.js comme le montre l’image ci-dessous.

![][1]

Vous pouvez l’utiliser pour des options

-   Spécifier le script de démarrage pour votre application Node.js (par exemple : /bin/server.js)
-   Spécifier le fichier de configuration PM2 à utiliser pour votre application Node.js (par exemple : /foo/process.json)

 >[AZURE.NOTE] Si vous souhaitez que votre processus de nœud de redémarrer automatiquement lorsque certains fichiers sont modifiés, vous devrez utiliser la configuration PM2. Dans le cas contraire votre application redémarre pas lorsqu’il reçoit les notifications de modification à partir des éléments tels que déploiement continue lorsque votre code de l’application change.

Vous pouvez vérifier la Node.js [processus fichier documentation](http://pm2.keymetrics.io/docs/usage/application-declaration/) pour toutes les options, mais voici un exemple de ce que vous utiliseriez en tant que votre fichier process.json

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Points importants à signaler dans cette configuration sont 

-   La propriété « script » spécifie script de démarrage de votre application.
-   La propriété « instances » Spécifie le nombre d’instances de processus nœud de lancement. Si vous exécutez votre application sur les tailles de machine virtuelle plus grandes plusieurs cœurs, que vous voulez optimiser vos ressources en définissant une valeur supérieure ici.
-   Le tableau « espion » indique tous les fichiers dont la modification vous souhaitez redémarrer votre processus de nœud.
-   Pour « watch_options », vous devez actuellement spécifier « usePolling » en tant que true en raison de la façon dont votre contenu de l’application est chargé.


## <a name="next-steps"></a>Étapes suivantes ##

* [Quel est le Service d’application sur Linux ?](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png