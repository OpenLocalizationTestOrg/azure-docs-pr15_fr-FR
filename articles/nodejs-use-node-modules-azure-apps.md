<properties
    pageTitle="Utilisation des Modules Node.js"
    description="Découvrez comment travailler avec les modules Node.js lors de l’utilisation de l’application Azure ou les Services Cloud."
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


# <a name="using-nodejs-modules-with-azure-applications"></a>Utilisation des Modules Node.js avec applications Azure

Ce document fournit des conseils sur l’utilisation de modules Node.js avec applications hébergées sur Azure. Il explique comment s’assurer que votre application utilise une version spécifique du module ainsi que l’utilisation de modules natifs avec Azure.

Si vous connaissez déjà à l’aide de modules Node.js, fichiers **package.json** et **npm shrinkwrap.json** , les conditions suivantes est un récapitulatif rapide de ce qui est décrit dans cet article :

* Azure Application Service comprend les fichiers **package.json** et **shrinkwrap.json de npm** et pouvez installer des modules en fonction des entrées dans ces fichiers.
* Les Services en nuage Azure attendre tous les modules soit installé sur l’environnement de développement et la **nœud\_modules** directory pour être inclus dans le cadre du package de déploiement. Il est possible d’activer la prise en charge pour l’installation de modules à l’aide des fichiers **package.json** ou **npm shrinkwrap.json** sur les Services en nuage, mais cela nécessite une personnalisation des scripts par défaut utilisés par les projets de Service Cloud. Pour obtenir un exemple de cette procédure, voir [démarrage Azure tâche pour exécuter installer npm pour éviter de déployer des modules nœud](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Machines virtuelles Azure ne sont pas traitées dans cet article, comme l’expérience de déploiement dans une machine virtuelle sera dépend du système d’exploitation hébergé par la Machine virtuelle.

##<a name="nodejs-modules"></a>Modules Node.js

Modules sont chargeables JavaScript qui fournissent des fonctionnalités spécifiques de votre application. Un module est généralement installé à l’aide de l’outil de ligne de commande **npm** , mais certains (par exemple, le module http) sont fournis dans le cadre du package Node.js core.

Lorsque les modules sont installés, ils sont stockés dans le **nœud\_modules** répertoire à la racine de votre structure du répertoire application. Chaque module au sein de la **nœud\_modules** directory gère sa propre **nœud\_modules** répertoire qui contient tous les modules qu’elle dépend, et ce processus se répète à nouveau pour chaque module tout en bas de la chaîne de dépendance. Ainsi, chaque module a été installé pour que sa propre version configuration requise pour les modules qu'elle dépend, cependant il peut entraîner tout à fait une structure de répertoire volumineux.

Lorsque vous déployez le **nœud\_modules** répertoire dans le cadre de votre application, il augmente la taille du déploiement par rapport à l’aide d’un fichier **package.json** ou **npm shrinkwrap.json** ; Toutefois, il ne garantit que la version des modules utilisées en production sont les mêmes que ceux de développement.

###<a name="native-modules"></a>Modules natifs

Alors que la plupart des modules sont des fichiers JavaScript simplement en texte brut, certains modules sont des images binaires spécifique à la plateforme. Ces modules sont compilées au moment de l’installation, généralement à l’aide de Python et nœud gyp. Dans la mesure où Azure Cloud Services sont basés sur la **nœud\_modules** dossier déployée dans le cadre du module application, tout natif inclus dans le cadre des modules installés devez travailler dans un service cloud en tant qu’il a été installé et compilé sur un système de développement Windows.

Azure Application Service ne prend pas en charge tous les modules natifs et peut échouer au compilation ceux dont les conditions préalables très spécifiques. Tandis que certains modules populaires comme MongoDB ont facultatifs dépendances natives et travail parfait sans les, deux solutions de contournement fait leurs preuves avec presque toutes les modules natifs disponibles aujourd'hui :

* Exécutez **npm installer** sur un ordinateur Windows qui comporte les conditions préalables pour tous les native du module installé. Ensuite, déployer créé **nœud\_modules** dossier dans le cadre de l’application de Service d’application Azure.
* Azure Application Service peut être configuré pour exécuter bash personnalisé ou des scripts shell pendant le déploiement, ce qui vous donne la possibilité d’exécuter des commandes personnalisées et de configurer précisément la manière **npm installer** est en cours d’exécution. Pour une vidéo montrant comment procéder, voir [Personnaliser les Scripts de déploiement de site Web avec Kudu].

###<a name="using-a-packagejson-file"></a>À l’aide d’un fichier package.json

Le fichier **package.json** consiste à spécifier les dépendances de niveau supérieur votre application nécessite la plateforme d’hébergement permettant d’installer les dépendances, plutôt que d’exiger vous permet d’inclure le **nœud\_packages** dossier dans le cadre du déploiement. Une fois que l’application a été déployée, la commande **npm installer** est utilisée pour analyser le fichier **package.json** et installez toutes les dépendances répertoriées.

Pendant le développement, vous pouvez utiliser la **--Enregistrer**, **--Enregistrer développement**, ou **--Enregistrer facultatif** paramètres lors de l’installation de modules pour ajouter une entrée pour le module à votre fichier **package.json** automatiquement. Pour plus d’informations, voir [installer npm](https://docs.npmjs.com/cli/install).

Un problème potentiel avec le fichier **package.json** est qu’il n'indique que la version des dépendances de niveau supérieur. Chaque module installé peut ou ne pouvez pas spécifier la version des modules qu'elle dépend, et il est possible que vous pouvez retrouver avec une chaîne de dépendance autre que celle utilisée dans le développement.

> [AZURE.NOTE]
> Lorsque vous déployez au Service d’application Azure, si votre fichier <b>package.json</b> fait référence à un module natif vous verrez une erreur similaire à ce qui suit lors de la publication de l’application à l’aide de Git :

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>À l’aide d’un fichier npm shrinkwrap.json

Le fichier **npm shrinkwrap.json** est une tentative pour résoudre les limites de contrôle de version module du fichier **package.json** . Alors que le fichier **package.json** comprend uniquement des versions pour les modules de niveau supérieur, le fichier **npm shrinkwrap.json** contient la configuration requise de version pour la chaîne de dépendance complet du module.

Lorsque votre application est prête pour la production, vous pouvez verrouiller-vers le bas de la version requise et créer un fichier de **npm shrinkwrap.json** à l’aide de la commande **npm film** . Utiliser les versions actuellement installées dans le **nœud\_modules** dossier et les enregistrer dans le fichier **npm shrinkwrap.json** . Une fois que l’application a été déployée dans l’environnement d’hébergement, la commande **npm installer** est utilisée pour analyser le fichier **npm shrinkwrap.json** et installez toutes les dépendances répertoriées. Pour plus d’informations, voir [npm film](https://docs.npmjs.com/cli/shrinkwrap).

> [AZURE.NOTE]
>Lorsque vous déployez au Service d’application Azure, si votre fichier <b>npm shrinkwrap.json</b> fait référence à un module natif vous verrez une erreur similaire à ce qui suit lors de la publication de l’application à l’aide de Git :

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez l’utilisation des modules Node.js avec Azure, découvrez comment [spécifier la version Node.js], [créer et déployer une application web Node.js]et [l’utilisation de l’Interface de ligne de commande Azure pour Mac et Linux].

Pour plus d’informations, voir le [Centre de développement Node.js](/develop/nodejs/).

[spécifier la version Node.js]: nodejs-specify-node-version-azure-apps.md
[Comment utiliser l’Interface de ligne de commande Azure pour Mac et Linux]: xplat-cli-install.md
[créer et déployer une application web Node.js]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Scripts de déploiement de site Web personnalisée avec Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
