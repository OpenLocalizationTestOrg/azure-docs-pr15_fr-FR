<properties 
    pageTitle="Python web applications avec bouteilles dans Azure" 
    description="Didacticiel présente à l’exécution d’une application web Python dans Azure Application Service Web Apps." 
    services="app-service\web" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/19/2016"
    ms.author="huvalo"/>


# <a name="creating-web-apps-with-bottle-in-azure"></a>Création d’applications web avec bouteilles dans Azure

Ce didacticiel décrit comment commencer en cours d’exécution Python dans Azure Application Service Web Apps. Applications Web fournit d’hébergement gratuit limitée et déploiement rapide, et vous pouvez utiliser Python ! Au fur et à mesure du votre application, vous pouvez basculer à l’hébergement payant et vous pouvez également intégrer avec tous les autres services Azure.

Vous allez créer une application web à l’aide de l’infrastructure web bouteilles (voir autres versions de ce didacticiel pour [Django](web-sites-python-create-deploy-django-app.md) et [ballon](web-sites-python-create-deploy-flask-app.md)). Vous créer l’application web à partir de la Azure Marketplace, configurez le déploiement Git et cloner le référentiel localement. Puis vous exécutez l’application web localement, apporter des modifications, valider et les pousser à [Azure Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Le didacticiel montre comment effectuer cette opération à partir de Windows ou Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="prerequisites"></a>Conditions préalables

- Windows, Mac ou Linux
- Python 2.7 ou 3.4
- setuptools, pip, virtualenv (2,7 Python uniquement)
- GIT
- [Outils Python 2.2 pour Visual Studio][] (PTVS) - Remarque : cette étape est facultative

**Remarque**: la publication TFS n’est actuellement pas pris en charge pour les projets Python.

### <a name="windows"></a>Windows

Si vous n’avez pas Python 2.7 ou 3.4 installés (32 bits), nous recommandons l’installation [Azure SDK pour Python 2.7] ou [Azure SDK pour Python 3.4] à l’aide de Web Platform Installer. Cette procédure installe la version 32 bits de Python, setuptools, pip, virtualenv, etc. (32 bits Python est ce qui est installé sur les ordinateurs hôtes Azure). Vous pouvez également obtenir Python à partir de [python.org].

Pour Git, nous vous recommandons de [Git pour Windows] ou [GitHub pour Windows]. Si vous utilisez Visual Studio, vous pouvez utiliser la prise en charge Git intégrée.

Nous recommandons également l’installation [Python outils 2.2 pour Visual Studio]. Cette étape est facultative, mais si vous avez [Visual Studio], y compris la gratuit Visual Studio Communauté 2013 ou Visual Studio Express 2013 pour le Web, puis cela vous donne un excellent IDE Python.

### <a name="maclinux"></a>Mac/Linux

Vous disposez Python et Git déjà installé, mais vérifiez que vous disposez Python 2.7 ou 3.4.


## <a name="web-app-creation-on-the-azure-portal"></a>Création de l’application Web sur le portail d’Azure

La première étape pour créer votre application consiste à créer l’application web via le [Portail Azure](https://portal.azure.com).  

1. Connectez-vous au portail Azure et cliquez sur le bouton **Nouveau** dans le coin inférieur gauche. 
3. Dans la zone de recherche, tapez « python ».
4. Dans les résultats de recherche, sélectionnez **bouteilles**, puis cliquez sur **créer**.
5. Configurer la nouvelle application bouteilles, telles que la création d’une nouvelle offre de Service d’application et un groupe de ressources de celui-ci. Ensuite, cliquez sur **créer**.
6. Configurer la publication Git pour votre application web nouvellement créé en suivant les instructions au [Déploiement Local de Git Azure Application Service](app-service-deploy-local-git.md).
 
## <a name="application-overview"></a>Vue d’ensemble de l’application

### <a name="git-repository-contents"></a>Contenu du référentiel GIT

Voici une vue d’ensemble des fichiers que vous trouverez dans le référentiel Git initial, nous allons cloner dans la section suivante.

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

Sources principales pour l’application. Se compose de 3 pages (index, sur contact) avec une mise en page maître.  Les scripts et le contenu statique incluent démarrage, jquery, modernizr et y répondre.

    \app.py

Prise en charge du serveur de développement local. Utilisez cette option pour exécuter l’application localement.

    \BottleWebProject.pyproj
    \BottleWebProject.sln

Fichiers de projet pour une utilisation avec [Python Tools pour Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy IIS pour les environnements virtuels et PTVS prise en charge de débogage distant.

    \requirements.txt

Lots externes requis par cette application. Le script de déploiement sera pip installer les packages répertoriés dans ce fichier.
 
    \web.2.7.config
    \web.3.4.config

Fichiers de configuration IIS. Le script de déploiement utilise le web.x.y.config approprié et copier sous web.config.

### <a name="optional-files---customizing-deployment"></a>Fichiers facultatifs - déploiement de personnalisation

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Fichiers facultatifs - Python runtime

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Fichiers supplémentaires sur le serveur

Certains fichiers hébergés sur le serveur, mais ne sont pas ajoutés au référentiel git. Ils sont créés par le script de déploiement.

    \web.config

Fichier de configuration IIS. Créé à partir de web.x.y.config dans chaque déploiement.

    \env\

Environnement virtuel Python. Créé au cours du déploiement si un environnement virtuel compatible n’existe pas déjà sur l’application web.  Packages répertoriés dans requirements.txt sont pip installé, mais pip ignore installation si les packages sont déjà installés.

Les 3 sections décrivent comment poursuivre le développement d’applications web sous 3 différents environnements :

- Fenêtres, avec des outils Python pour Visual Studio
- Windows, avec ligne de commande
- Mac/Linux avec ligne de commande


## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Web application développement - Windows - Python Tools pour Visual Studio

### <a name="clone-the-repository"></a>Cloner le référentiel

Tout d’abord, cloner le référentiel à l’aide de l’url fournie dans le portail Azure. Pour plus d’informations, voir [Le déploiement Local Git Azure Application Service](app-service-deploy-local-git.md).

Ouvrez le fichier solution (.sln) qui est inclus dans la racine du référentiel.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### <a name="create-virtual-environment"></a>Créer l’environnement virtuel

Nous allons maintenant créer un environnement virtuel pour le développement local. Dans **Les environnements Python** sélectionner **Ajouter environnement virtuel...**menu contextuel.

- Vérifiez que le nom de l’environnement est `env`.

- Sélectionnez le relais de base. Assurez-vous d’utiliser la même version de Python est activée pour votre application web (dans runtime.txt ou la carte de **Paramètres de l’Application** de votre application web dans le portail Azure).

- Assurez-vous que l’option Télécharger et installer des packages est cochée.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

Cliquez sur **créer**. Cela créer l’environnement virtuel et installer les dépendances répertoriées dans requirements.txt.

### <a name="run-using-development-server"></a>Exécuter à l’aide du serveur de développement

Appuyez sur F5 pour démarrer le débogage et votre navigateur web seront ouvre automatiquement à la page en cours d’exécution localement.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

Vous pouvez définir des points d’arrêt dans les sources, utilisez les fenêtres Espion, etc.. Voir les [Outils Python pour la Documentation de Visual Studio] pour plus d’informations sur les différentes fonctionnalités.

### <a name="make-changes"></a>Apporter des modifications

Désormais, vous pouvez essayer en apportant des modifications aux sources d’application et/ou aux modèles.

Une fois que vous avez testé vos modifications, les valider au référentiel Git :

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### <a name="install-more-packages"></a>Installer des packages

Votre application peut-être avoir des dépendances au-delà de Python et bouteilles.

Vous pouvez installer des packages supplémentaires à l’aide de pip. Pour installer un package, avec le bouton droit sur l’environnement virtuel, puis sélectionnez **Installer un Package Python**.

Par exemple, pour installer le Kit de développement Azure pour Python, qui permet d’accéder à stockage Azure, bus des services et d’autres services Azure, entrez `azure`:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

Avec le bouton droit sur l’environnement virtuel, puis sélectionnez **Générer requirements.txt** pour mettre à jour requirements.txt.

Ensuite, validez les modifications à requirements.txt vers le référentiel Git.

### <a name="deploy-to-azure"></a>Déploiement d’Azure

Pour déclencher la lecture d’un déploiement, cliquez sur **synchronisation** ou **transmission**. Synchronisation effectue un push et une extraction.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

Le premier déploiement prend un certain temps, qu’il crée un environnement virtuel, packages d’installation, etc..

Visual Studio n’affiche pas la progression du déploiement. Si vous souhaitez vérifier le résultat, consultez la section sur la [résolution des problèmes - déploiement](#troubleshooting-deployment).

Accédez à l’URL Azure pour visualiser vos modifications.


## <a name="web-app-development---windows---command-line"></a>Ligne de commande - Windows - développement d’applications Web

### <a name="clone-the-repository"></a>Cloner le référentiel

Tout d’abord, cloner le référentiel à l’aide de l’URL fournie dans le portail Azure, puis ajoutez le référentiel Azure une télécommande. Pour plus d’informations, voir [Le déploiement Local Git Azure Application Service](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Créer l’environnement virtuel

Nous allons créer un nouvel environnement virtuel pour le développement (ne pas l’ajoutez au référentiel). Environnements virtuels dans Python ne sont pas transférable, afin que tous les développeurs travaillant sur l’application allez créer leurs propres localement.

Assurez-vous d’utiliser la même version de Python est activée pour votre application web (dans runtime.txt ou la carte de paramètres de l’Application pour votre application web dans le portail Azure)

Pour Python 2.7 :

    c:\python27\python.exe -m virtualenv env

Pour Python 3.4 :

    c:\python34\python.exe -m venv env

Installer les packages externes requises par votre application. Vous pouvez utiliser le fichier requirements.txt à la racine du référentiel pour installer les packages dans votre environnement virtuel :

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>Exécuter à l’aide du serveur de développement

Vous pouvez lancer l’application sous un serveur de développement avec la commande suivante :

    env\scripts\python app.py

La console affiche l’URL et écoute port du serveur :

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

Ensuite, ouvrez votre navigateur web à cette URL.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### <a name="make-changes"></a>Apporter des modifications

Désormais, vous pouvez essayer en apportant des modifications aux sources d’application et/ou aux modèles.

Une fois que vous avez testé vos modifications, les valider au référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installer des packages

Votre application peut-être avoir des dépendances au-delà de Python et bouteilles.

Vous pouvez installer des packages supplémentaires à l’aide de pip. Par exemple, pour installer le Kit de développement Azure pour Python, qui permet d’accéder à stockage Azure, bus des services et d’autres services Azure, tapez :

    env\scripts\pip install azure

Veillez à mettre à jour requirements.txt :

    env\scripts\pip freeze > requirements.txt

Valider les modifications :

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Déploiement d’Azure

Pour déclencher la lecture d’un déploiement, diffuser les changements à Azure :

    git push azure master

Vous verrez le résultat du script de déploiement, y compris la création d’un environnement virtuel, l’installation des packages, la création du fichier web.config.

Accédez à l’URL Azure pour visualiser vos modifications.


## <a name="web-app-development---maclinux---command-line"></a>Ligne de commande de développement - Mac/Linux - Web app

### <a name="clone-the-repository"></a>Cloner le référentiel

Tout d’abord, cloner le référentiel à l’aide de l’URL fournie dans le portail Azure, puis ajoutez le référentiel Azure une télécommande. Pour plus d’informations, voir [Le déploiement Local Git Azure Application Service](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Créer l’environnement virtuel

Nous allons créer un nouvel environnement virtuel pour le développement (ne pas l’ajoutez au référentiel). Environnements virtuels dans Python ne sont pas transférable, afin que tous les développeurs travaillant sur l’application allez créer leurs propres localement.

Assurez-vous d’utiliser la même version de Python est activée pour votre application web (dans runtime.txt ou la carte de paramètres de l’Application de votre application web dans le portail Azure).

Pour Python 2.7 :

    python -m virtualenv env

Pour Python 3.4 :

    python -m venv env
ou pyvenv environnement

Installer les packages externes requises par votre application. Vous pouvez utiliser le fichier requirements.txt à la racine du référentiel pour installer les packages dans votre environnement virtuel :

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>Exécuter à l’aide du serveur de développement

Vous pouvez lancer l’application sous un serveur de développement avec la commande suivante :

    env/bin/python app.py

La console affiche l’URL et écoute port du serveur :

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

Ensuite, ouvrez votre navigateur web à cette URL.

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### <a name="make-changes"></a>Apporter des modifications

Désormais, vous pouvez essayer en apportant des modifications aux sources d’application et/ou aux modèles.

Une fois que vous avez testé vos modifications, les valider au référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installer des packages

Votre application peut-être avoir des dépendances au-delà de Python et bouteilles.

Vous pouvez installer des packages supplémentaires à l’aide de pip. Par exemple, pour installer le Kit de développement Azure pour Python, qui permet d’accéder à stockage Azure, bus des services et d’autres services Azure, tapez :

    env/bin/pip install azure

Veillez à mettre à jour requirements.txt :

    env/bin/pip freeze > requirements.txt

Valider les modifications :

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Déploiement d’Azure

Pour déclencher la lecture d’un déploiement, diffuser les changements à Azure :

    git push azure master

Vous verrez le résultat du script de déploiement, y compris la création d’un environnement virtuel, l’installation des packages, la création du fichier web.config.

Accédez à l’URL Azure pour visualiser vos modifications.


## <a name="troubleshooting---package-installation"></a>Résolution des problèmes - Installation du Package

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Résolution des problèmes - environnement virtuel

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## <a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur les outils de Python et bouteilles pour Visual Studio : 
 
- [Bouteilles Documentation]
- [Outils Python pour la Documentation de Visual Studio]

Pour plus d’informations sur l’utilisation de stockage de Table Azure et MongoDB :

- [Bouteilles et MongoDB sur Azure avec Python Tools pour Visual Studio]
- [Bouteilles et le stockage de Table Azure sur Azure avec Python Tools pour Visual Studio]

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Bouteilles et MongoDB sur Azure avec Python Tools pour Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md
[Bouteilles et le stockage de Table Azure sur Azure avec Python Tools pour Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md

<!--External Link references-->
[Azure SDK pour Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK pour Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[Python.org]: http://www.python.org/
[GIT pour Windows]: http://msysgit.github.io/
[GitHub pour Windows]: https://windows.github.com/
[Outils Python pour Visual Studio]: http://aka.ms/ptvs
[Python 2.2 des outils pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Outils Python pour la Documentation de Visual Studio]: http://aka.ms/ptvsdocs 
[Bouteilles Documentation]: http://bottlepy.org/docs/dev/index.html
 
