<properties 
    pageTitle="Configuration Python avec Azure Application Service Web Apps" 
    description="Ce didacticiel décrit les options pour la création et la configuration d’un serveur Web simple application Python conforme à l’Interface de passerelle (WSGI) sur Azure Application Service Web Apps." 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/26/2016" 
    ms.author="huvalo"/>




# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configuration Python avec Azure Application Service Web Apps

Ce didacticiel décrit les options pour la création et la configuration d’une application Python compatible Web serveur passerelle Interface (WSGI) base sur [Azure Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Il décrit les fonctionnalités supplémentaires du déploiement Git, telles que l’environnement virtuel et d’installation d’un package à l’aide de requirements.txt.


## <a name="bottle-django-or-flask"></a>Bouteilles, Django ou ballon ?

La Azure Marketplace contient des modèles pour les structures bouteilles, Django et ballon. Si vous développez votre application web première dans le Service d’application Azure ou si vous n’êtes pas familiarisé avec Git, nous vous recommandons de choisir l’une de ces didacticiels, qui incluent des instructions étape par étape pour la création d’une application de travail à partir de la galerie à l’aide de déploiement Git à partir de Windows ou Mac :

- [Création d’applications web avec bouteilles](web-sites-python-create-deploy-bottle-app.md)
- [Création d’applications web avec Django](web-sites-python-create-deploy-django-app.md)
- [Création d’applications web avec ballon](web-sites-python-create-deploy-flask-app.md)


## <a name="web-app-creation-on-azure-portal"></a>Création de l’application Web sur le portail Azure

Ce didacticiel suppose l’abonnement Azure existant et accéder au portail Azure.

Si vous ne disposez pas d’une application web existante, vous pouvez créer un à partir du [Portail Azure](https://portal.azure.com).  Cliquez sur le bouton Nouveau dans le coin supérieur gauche, puis cliquez sur **Web + Mobile** > **dans le navigateur**.

## <a name="git-publishing"></a>Publication GIT

Configurer la publication Git pour votre application web nouvellement créé en suivant les instructions au [Déploiement Local de Git Azure Application Service](app-service-deploy-local-git.md). Ce didacticiel utilise Git pour créer, gérer et publier notre application web Python Azure application service.

Une fois la publication Git a été configurée, un référentiel Git sera créé et associé à votre application web. URL du référentiel s’affichera et peut désormais être utilisé pour déplacer des données à partir de l’environnement de développement local vers le cloud. Pour publier des applications via Git, assurez-vous qu’un client Git est également installé et suivez les instructions fournies pour distribuer le contenu de votre application web au Service d’application Azure.


## <a name="application-overview"></a>Vue d’ensemble de l’application

Dans les sections suivantes, les fichiers suivants sont créés. Ils doivent être placés à la racine du référentiel Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Gestionnaire de WSGI

WSGI est une norme Python décrite par [PEP 3333](http://www.python.org/dev/peps/pep-3333/) définition d’une interface entre le serveur web et Python. Il fournit une interface standard pour écrire des différentes applications web et des structures à l’aide de Python. Structures de web populaires Python aujourd'hui utilisent WSGI. Azure permet d’application Service Web Apps que vous aider pour n’importe quel ces concepts ; en outre, les utilisateurs expérimentés peuvent même créer leurs propres tant que gestionnaire personnalisé suit les instructions de la spécification WSGI.

Voici un exemple d’un `app.py` qui définit un gestionnaire personnalisé :

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Vous pouvez exécuter cette application localement avec `python app.py`, puis naviguez jusqu’au `http://localhost:5555` dans votre navigateur web.


## <a name="virtual-environment"></a>Environnement virtuel

Bien que l’application d’exemple ci-dessus ne nécessite pas de tous les packages externe, il est probable que votre application nécessite certaines.

Pour aider à gérer les dépendances externes package, déploiement d’Azure Git prend en charge la création des environnements virtuels.

Quand Azure détecte un requirements.txt à la racine du référentiel, qu’il crée automatiquement un environnement virtuel nommé `env`. Cela se produit uniquement sur le premier déploiement, ou pendant un déploiement après les Python sélectionné runtime a changé.

Vous souhaiterez probablement créer un environnement virtuel localement pour le développement, mais ne pas inclure dans votre référentiel Git.


## <a name="package-management"></a>Gestion des packages

Packages répertoriés dans requirements.txt seront automatiquement installés dans l’environnement virtuel à l’aide de pip. Cela se produit dans chaque déploiement, mais pip ignore installation si un package est déjà installé.

Exemple `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Version Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exemple `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config

Vous devez créer un fichier web.config pour spécifier comment le serveur doit gérer les demandes.

Notez que si vous avez un fichier web.x.y.config dans votre référentiel, où reposent correspond à l’exécution de Python sélectionné, puis Azure copie automatiquement le fichier en tant que web.config approprié.

Les exemples suivants web.config s’appuient sur un script de proxy environnement virtuel, qui est décrit dans la section suivante.  Ils fonctionnent avec le gestionnaire WSGI utilisé dans l’exemple `app.py` ci-dessus.

Exemple `web.config` pour Python 2.7 :

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Exemple `web.config` pour Python 3.4 :

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Fichiers statiques doivent être gérés par le serveur web directement, sans passer par code Python, pour améliorer les performances.

Dans les exemples ci-dessus, l’emplacement des fichiers sur le disque statiques doit correspondre à l’emplacement dans l’URL. Cela signifie qu’une demande de `http://pythonapp.azurewebsites.net/static/site.css` servira le fichier sur le disque à `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER`est l’endroit où vous spécifiez le gestionnaire WSGI. Dans les exemples ci-dessus, il a `app.wsgi_app` , car le gestionnaire est une fonction nommée `wsgi_app` dans `app.py` dans le dossier racine.

`PYTHONPATH`peut être personnalisée, mais si vous installez toutes vos dépendances dans l’environnement virtuel en les spécifiant dans requirements.txt, vous devez ne doivent pas le modifier.


## <a name="virtual-environment-proxy"></a>Environnement virtuel Proxy

Le script suivant est utilisé pour récupérer le gestionnaire WSGI, activer le virtuel environnement et journal des erreurs. Il est conçu pour être générique et utilisés sans modification.

Contenu de `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Personnaliser Git déploiement

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## <a name="troubleshooting---package-installation"></a>Résolution des problèmes - Installation du Package

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Résolution des problèmes - environnement virtuel

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement Python](/develop/python/).

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)





 
