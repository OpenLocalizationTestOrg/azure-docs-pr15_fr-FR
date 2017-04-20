<properties 
    pageTitle="Comment utiliser io.js avec Azure Application Service Web Apps" 
    description="Découvrez comment utiliser une application web dans le Service d’application Azure avec io.js." 
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
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Comment utiliser io.js avec Azure Application Service Web Apps

branche de populaires nœud [io.js] propose plusieurs différences au projet Node.js de Joyent, y compris un modèle de gouvernance plus ouvert, un cycle de publication plus rapide et une adoption plus rapide de nouvelles fonctionnalités JavaScript expériences.

[Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps propose plusieurs versions Node.js préinstallées, il permet également d’un fichier binaire Node.js fourni par l’utilisateur. Cet article décrit deux méthodes activer l’utilisation de io.js dans des applications Web Application Service : l’utilisation d’un script de déploiement étendu, qui configure automatiquement Azure pour utiliser la dernière version disponible io.js, ainsi que le téléchargement manuel d’un io.js binaire. 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>À l’aide d’un Script de déploiement

Lors du déploiement d’une application Node.js, application de Service Web Apps s’exécute un certain nombre de commandes petites pour vous assurer que l’environnement est correctement configuré. À l’aide d’un script de déploiement, ce processus peut être personnalisé pour inclure le téléchargement et la configuration de io.js.

[io.js Script de déploiement](https://github.com/felixrieseberg/iojs-azure) est disponible sur GitHub. Pour activer io.js dans votre application web, copiez simplement **.deployment**, **deploy.cmd** et **IISNode.yml** à la racine de votre dossier d’application et déployer des applications Web.  

Le premier fichier, **.deployment**, demande Web Apps pour exécuter **deploy.cmd** lors du déploiement. Ce script s’exécute toutes les étapes habituelles pour une application Node.js, mais télécharge également la dernière version d’io.js. Enfin, **IISNode.yml** configure les applications Web pour utiliser uniquement la io.js téléchargé binaire au lieu d’un fichier binaire Node.js préinstallé.

> [AZURE.NOTE] Mettre à jour le fichier binaire io.js utilisés, simplement redéployez votre application - le script allez télécharger une nouvelle version d’io.js chaque fois unique déploiement de l’application.

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>À l’aide d’une Installation manuelle

L’installation manuelle d’une version io.js personnalisé inclut les étapes que deux. Téléchargez tout d’abord **win x64** binaire directement à partir de la [distribution io.js]. Obligatoire sont deux fichiers - **iojs.exe** et **iojs.lib**. Enregistrer les fichiers dans un dossier au sein de votre application web, par exemple dans la **Corbeille/iojs**.

Pour configurer les applications Web pour utiliser **iojs.exe** au lieu d’une version de nœud préinstallée, créez un fichier **IISNode.yml** à la racine de votre application et ajoutez la ligne suivante.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>Étapes suivantes

Dans cet article que vous avez appris comment utiliser io.js avec application de Service Web Apps, à l’aide fournie scripts de déploiement installation également comme manuelle. 

> [AZURE.NOTE] IO.js est en cours de développement dense et mises à jour plus fréquemment que Node.js. Un certain nombre de modules Node.js peut ne pas fonctionne avec io.js - Veuillez consulter [io.js sur GitHub] de résoudre les problèmes.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

[IO.js]: https://iojs.org
[distribution IO.js]: https://iojs.org/dist/
[IO.js sur GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 