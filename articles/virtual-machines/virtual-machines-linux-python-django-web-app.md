<properties 
    pageTitle="Python web app avec Django sur Linux | Microsoft Azure" 
    description="Découvrez comment héberger une application web basée sur les Django sur Azure à l’aide d’une machine virtuelle Linux." 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Application web Django Bonjour un Linux VM

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

Ce didacticiel décrit comment héberger un site Web en fonction de Django sur Microsoft Azure à l’aide d’une machine virtuelle Linux. Ce didacticiel suppose que vous n’avez aucune expérience préalable à l’aide d’Azure. À la fin de ce guide, vous aurez une application basée sur les Django vers le haut et en cours d’exécution dans le cloud.

Vous allez apprendre comment :

* Configurer une machine virtuelle Azure à l’hôte Django. Tandis que ce didacticiel explique la procédure de cette phase sous **Linux**, le même pourrait également être effectué avec une machine virtuelle Server de Windows hébergé dans Azure. 
* Créer une nouvelle application Django de Linux.

En suivant ce didacticiel, vous allez générer une application web Hello World simple. L’application sera hébergée sur une machine virtuelle Azure.

Capture d’écran de l’application terminée est inférieur à :

![Une fenêtre de navigateur affichant la page du monde hello sur Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Créer et configurer une machine virtuelle Azure à l’hôte Django

1. Suivez les instructions donné [ici](virtual-machines-linux-quick-create-portal.md) pour créer une machine virtuelle Azure de la distribution *Ubuntu Server 14.04 LTS* .  Si vous préférez, vous pouvez choisir d’authentification de mot de passe au lieu de clé publique SSH.

1. Modifier le groupe de sécurité de réseau pour autoriser le trafic http entrant vers le port 80 en suivant les instructions [ici](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Par défaut, votre nouvelle machine virtuelle n’est un nom de domaine complet (FQDN).  Vous pouvez en créer une en suivant les instructions [ici](virtual-machines-linux-portal-create-fqdn.md).  Cette étape est facultative effectuer ce didacticiel.

## <a id="setup"> </a>La configuration de l’environnement de développement

**Remarque :** Si vous devez installer Python ou que vous voulez utiliser les bibliothèques du Client, voir le [Guide d’Installation Python](../python-how-to-install.md).

La machine virtuelle Linux Ubuntu est déjà fourni avec 2.7 Python préinstallés, mais elle n’a pas Apache ou django installé.  Suivez ces étapes pour vous connecter à votre ordinateur virtuel et installer Apache et django.

1.  Ouvrir une nouvelle fenêtre **Terminal** .
    
1.  Entrez la commande suivante pour vous connecter à la machine virtuelle Azure.  Si vous n’avez pas créé un nom de domaine complet, vous pouvez vous connecter à l’aide de l’adresse IP affichée dans la machine virtuelle résumée dans le portail classique Azure.

        $ ssh yourusername@yourVmUrl

1.  Entrez les commandes suivantes pour installer django :

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  Entrez la commande suivante pour installer Apache avec mod wsgi :

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>Création d’une application Django

1.  Ouvrez la fenêtre **Terminal** que vous avez utilisées dans la section précédente pour ssh votre machine virtuelle.
    
1.  Entrez les commandes suivantes pour créer un nouveau projet Django :

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    Le script **django-Admin.py** génère une structure de base pour des sites Web Django :
    -   **HelloWorld/Manage.py** vous aide à démarrer d’hébergement et arrêter l’hébergement de votre site Web Django
    -   **HelloWorld/HelloWorld/Settings.py** contient les paramètres de Django pour votre application.
    -   **HelloWorld/HelloWorld/URLs.py** contient le code de mappage entre chaque url et sa vue.

1.  Créer un nouveau fichier nommé **views.py** dans le répertoire **/var/www/helloworld/helloworld** . Ceci contient l’affichage qui rend à la page « Bonjour tout le monde ». Démarrez votre éditeur et entrez les informations suivantes :
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Maintenant, remplacez le contenu du fichier **urls.py** avec les éléments suivants :

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>La configuration d’Apache

1.  Créer un fichier de configuration Apache hôte virtuel **/etc/apache2/sites-available/helloworld.conf**. Définir le contenu à l’exemple suivant et remplacez *yourVmName* par le nom réel de l’ordinateur que vous utilisez (par exemple *pyubuntu*).

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  Activer le site avec la commande suivante :

        $ sudo a2ensite helloworld

1.  Redémarrez Apache avec la commande suivante :

        $ sudo service apache2 reload

1.  Enfin, chargez la page web dans votre navigateur :

    ![Une fenêtre de navigateur affichant la page du monde hello sur Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>Arrêter votre machine virtuelle Azure

Lorsque vous avez terminé avec ce didacticiel, d’arrêt et/ou supprimer votre machine virtuelle Azure nouvellement créé pour libérer des ressources pour d’autres didacticiels et éviter entraîner de frais d’utilisation Azure.
