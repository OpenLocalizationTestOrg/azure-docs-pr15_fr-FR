<properties
    pageTitle="Python web app avec Django | Microsoft Azure"
    description="Ce didacticiel vous apprend à héberger un site Web basée sur Django sur Azure à l’aide d’une machine virtuelle Windows Server 2012 R2 centre de données à l’aide du modèle de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Application web Django Hello World sur une machine virtuelle de Windows Server

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Ce didacticiel décrit l’hébergement d’un site Web en fonction de Django sur Microsoft Azure à l’aide d’une machine virtuelle Windows Server. Ce didacticiel suppose que vous n’avez aucune expérience préalable à l’aide d’Azure. Au terme de ce didacticiel, vous aurez une application basée sur les Django vers le haut et en cours d’exécution dans le cloud.

Vous allez apprendre comment :

* Configurer une machine virtuelle Azure à l’hôte Django. Tandis que ce didacticiel explique la procédure de cette phase sous Windows Server, le même peut également être effectué avec une machine virtuelle hébergé dans Azure Linux.
* Créer une nouvelle application Django à partir de Windows.

En suivant ce didacticiel, vous allez générer une application web Hello World simple. L’application sera hébergée sur une machine virtuelle Azure.

Capture d’écran de l’application terminée s’affiche ensuite.

![Une fenêtre de navigateur affichant la page du monde hello sur Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Créer et configurer une machine virtuelle Azure à l’hôte Django

1. Suivez les instructions donné [ici](virtual-machines-windows-classic-tutorial.md) pour créer une machine virtuelle Azure de la distribution de Windows Server 2012 R2 centre de données.

1. Demander à Azure afin de rediriger le trafic du port 80 à partir du web au port 80 sur l’ordinateur virtuel :
 - Accédez à votre machine virtuelle nouvellement créé dans le portail classique Azure, puis cliquez sur l’onglet **points de terminaison** .
 - Cliquez sur le bouton **Ajouter** en bas de l’écran.
    ![ajouter le point de terminaison](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Ouvrez le protocole **TCP** **80 PORT PUBLIC** dans le cadre de **privé PORT 80**.
![][port80]
1. Sous l’onglet **tableau de bord** , cliquez sur **se connecter** pour utiliser **Bureau à distance** pour vous connecter à distance à la machine virtuelle Azure nouvellement créée.  

**Note importante :** Toutes les instructions ci-dessous part du principe que correctement connecté à la machine virtuelle et émettant des commandes il plutôt que votre ordinateur local.

## <a id="setup"> </a>L’installation Python, Django, WFastCGI

**Remarque :** Afin de télécharger à l’aide d’Internet Explorer, vous devrez peut-être configurer les paramètres d’Internet Explorer ÉCHAP (début/administration outils/Manager/Local serveur, puis cliquez sur **La Configuration de sécurité renforcée d’Internet Explorer**, définissez pour désactiver).

1. Installer la dernière 2.7 Python ou 3.4 à partir de [python.org][].
1. Installez les packages wfastcgi et django à l’aide de pip.

    Pour Python 2.7, utilisez la commande suivante.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Pour Python 3.4, utilisez la commande suivante.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>L’installation d’IIS avec FastCGI

1. Installez IIS avec FastCGI prise en charge.  Cela peut prendre plusieurs minutes pour exécuter.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Création d’une application Django

1.  À partir de *C:\inetpub\wwwroot*, entrez la commande suivante pour créer un projet Django :

    Pour Python 2.7, utilisez la commande suivante.

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Pour Python 3.4, utilisez la commande suivante.

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Le résultat de la commande Nouveau AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  La commande **django-admin** génère une structure de base pour des sites Web Django :

  -   **helloworld\manage.py** vous aide à démarrer d’hébergement et arrêter l’hébergement de votre site Web Django
  -   **helloworld\helloworld\settings.py** contient les paramètres de Django pour votre application.
  -   **helloworld\helloworld\urls.py** contient le code de mappage entre chaque url et sa vue.

1.  Créer un nouveau fichier nommé **views.py** dans le répertoire *C:\inetpub\wwwroot\helloworld\helloworld* . Ceci contient l’affichage qui rend à la page « Bonjour tout le monde ». Démarrez votre éditeur et entrez les informations suivantes :

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Remplacez le contenu du fichier urls.py avec les éléments suivants.

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>Configuration des services Internet

1. Déverrouiller la section gestionnaires dans la applicationhost.config global.  Cela permettra l’utilisation du gestionnaire python dans votre fichier web.config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Activer WFastCGI.  Cette action ajoute une application à la applicationhost.config global qui fait référence à votre relais Python exécutable et le script wfastcgi.py.

    Python 2.7 :

        c:\python27\scripts\wfastcgi-enable

    Python 3.4 :

        c:\python34\scripts\wfastcgi-enable

1. Créer un fichier web.config dans *C:\inetpub\wwwroot\helloworld*.  La valeur de la `scriptProcessor` attribut doit correspondre à la sortie de l’étape précédente.  Consultez la page pour [wfastcgi][] sur pypi pour d’autres paramètres wfastcgi activé.

    Python 2.7 :

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4 :

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Mettre à jour l’emplacement du IIS par défaut Site Web pour qu’il pointe vers le dossier de projet django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Enfin, chargez la page web dans votre navigateur.

![Une fenêtre de navigateur affichant la page du monde hello sur Azure][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>Arrêter votre machine virtuelle Azure

Lorsque vous avez terminé avec ce didacticiel, arrêtez et/ou supprimer votre machine virtuelle Azure nouvellement créé pour libérer des ressources pour d’autres didacticiels et éviter d’entraîner de frais d’utilisation Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
