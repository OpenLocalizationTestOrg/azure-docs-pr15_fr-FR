<properties
    pageTitle="Rôles web et collaborateur PHP | Microsoft Azure"
    description="Un guide de création de rôles web et collaborateur PHP dans un service cloud Azure et configurez l’exécution de PHP."
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>Comment créer des rôles web et collaborateur PHP

## <a name="overview"></a>Vue d’ensemble

Ce guide vous montrent comment créer des rôles web ou collaborateur PHP dans un environnement de développement Windows, choisissez une version spécifique de PHP dans les versions « prédéfinies » disponibles, modifier la configuration PHP, activer les extensions et enfin, déployer sur Azure. Il décrit également comment configurer un rôle web ou de travail pour utiliser un runtime PHP (avec configuration personnalisée et extensions) que vous spécifiez.

## <a name="what-are-php-web-and-worker-roles"></a>Quels sont les rôles web et collaborateur PHP ?

Azure fournit trois calculent des modèles pour les applications en cours d’exécution : Azure Application Service et Machines virtuelles Azure Azure Cloud Services. Les trois modèles prennent en charge PHP. Services en nuage, qui inclut les rôles web et collaborateur, fournit la *plateforme en tant que service (PaaS)*. Au sein d’un service cloud, un rôle web fournit un serveur web Internet Information Services (IIS) dédié pour héberger des applications web frontaux. Un rôle de collaborateur peut exécuter asynchrones, longue ou des tâches indépendamment d’interaction de l’utilisateur ou d’entrée.

Pour plus d’informations sur ces options, voir [options d’hébergement fournies par Azure de calcul](./cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Téléchargez le Kit de développement Azure pour PHP

Le [Kit de développement logiciel Azure pour PHP] se compose de plusieurs composants. Cet article utilisera deux d'entre eux : Azure PowerShell et les émulateurs Azure. Ces deux composants peuvent être installés via Microsoft Web Platform Installer. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](powershell-install-configure.md).

## <a name="create-a-cloud-services-project"></a>Créer un projet de Services Cloud

La première étape de création d’un rôle PHP web ou de travail consiste à créer un projet de Service Azure. un projet de Service Azure sert un conteneur logique pour les rôles web et de travail, et qu’il contient des fichiers de [définition de service (.csdef)] et [la configuration du service (.cscfg)] du projet.

Pour créer un nouveau projet de Service Azure, exécutez Azure PowerShell en tant qu’administrateur, puis exécutez la commande suivante :

    PS C:\>New-AzureServiceProject myProject

Cette commande crée un nouveau répertoire (`myProject`) à laquelle vous pouvez ajouter des rôles web et de travail.

## <a name="add-php-web-or-worker-roles"></a>Ajouter des rôles web ou collaborateur PHP

Pour ajouter un rôle web PHP à un projet, exécutez la commande suivante à partir de répertoire racine du projet :

    PS C:\myProject> Add-AzurePHPWebRole roleName

Pour un rôle de collaborateur, utilisez cette commande :

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] La `roleName` paramètre est facultatif. S’il est omis, le nom de rôle sera généré automatiquement. Le premier rôle web créé sera `WebRole1`, le deuxième élément sera `WebRole2`, et ainsi de suite. Le rôle de collaborateur première créé sera `WorkerRole1`, le deuxième élément sera `WorkerRole2`, et ainsi de suite.

## <a name="specify-the-built-in-php-version"></a>Spécifier la version PHP intégrée

Lorsque vous ajoutez un rôle PHP web ou de travail à un projet, les fichiers de configuration du projet sont modifiées afin que PHP sera installé sur chaque instance web ou de travail de votre application lorsqu’elle est déployée. Pour afficher la version de PHP qui est installé par défaut, exécutez la commande suivante :

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Le résultat de la commande ci-dessus est semblable à celui présenté ci-dessous. Dans cet exemple, le `IsDefault` indicateur est défini sur `true` pour PHP 5.3.17, indiquant qu’il soit la version PHP par défaut installée.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Vous pouvez définir la version du runtime PHP à une des versions PHP répertoriés. Par exemple, pour définir la version de PHP (pour un rôle avec le nom `roleName`) à 5.4.0, utilisez la commande suivante :

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Versions PHP disponibles peuvent changer à l’avenir.

## <a name="customize-the-built-in-php-runtime"></a>Personnaliser le runtime PHP intégré

Vous contrôlez la configuration du runtime PHP qui est installé lorsque vous suivez les étapes ci-dessus, y compris la modification de `php.ini` paramètres et l’activation des extensions.

Pour personnaliser le runtime PHP intégré, procédez comme suit :

1. Ajouter un nouveau dossier, nommé `php`, à la `bin` répertoire de votre rôle web. Pour un rôle de collaborateur, ajoutez-le au répertoire racine du rôle.
2. Dans la `php` dossier, créez un autre dossier nommé `ext`. Placez aucune `.dll` les fichiers d’extension (par exemple, `php_mongo.dll`) que vous souhaitez activer dans ce dossier.
3. Ajouter un `php.ini` de fichiers vers le `php` dossier. Activer toutes les extensions personnalisées et définissez les directives PHP dans ce fichier. Par exemple, si vous souhaitez activer `display_errors` sur et activer la `php_mongo.dll` extension, le contenu de votre `php.ini` fichier se présente comme suit :

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] Tous les paramètres que vous ne définissez pas explicitement dans la `php.ini` fichier que vous fournissez seront automatiquement être définis sur leurs valeurs par défaut. Toutefois, n’oubliez pas que vous pouvez ajouter un complète `php.ini` fichier.

## <a name="use-your-own-php-runtime"></a>Utiliser votre propre runtime PHP
Dans certains cas, au lieu de sélection d’un runtime PHP intégré et configurez-le comme décrit ci-dessus, vous souhaiterez fournir votre propre runtime PHP. Par exemple, vous pouvez utiliser le même runtime PHP dans un rôle web ou de travail que vous utilisez dans votre environnement de développement. Cela facilite pour vous assurer que l’application ne change pas le comportement de votre environnement de production.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurer un rôle web pour utiliser votre propre PHP d’exécution

Pour configurer un rôle web pour utiliser un runtime PHP que vous fournissez, procédez comme suit :

1. Créer un projet de Service Azure et ajoutez un rôle web PHP comme décrit plus haut dans cette rubrique.
2. Créer un `php` dossier dans le `bin` dossier dans lequel se trouve dans le répertoire racine de votre rôle de serveur web et ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) à la `php` dossier.
3. (FACULTATIF) Si votre runtime PHP utilise les [Pilotes de Microsoft pour PHP pour SQL Server][sqlsrv drivers], vous devrez configurer votre rôle web pour installer [SQL Server Native Client 2012] [ sql native client] lorsqu’il est mis en service. Pour ce faire, ajoutez l' [installer sqlncli.msi x64] à la `bin` dossier dans le répertoire racine de votre rôle de serveur web. Le script de démarrage décrit dans l’étape suivante s’exécutera automatiquement le programme d’installation lorsque le rôle est mis en service. Si votre runtime PHP n’utilise pas le Microsoft Drivers pour PHP pour SQL Server, vous pouvez supprimer la ligne suivante à partir du script indiqué dans l’étape suivante :

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Définir une tâche de démarrage qui configure [Internet Information Services (IIS)] [ iis.net] à utiliser votre runtime PHP pour gérer les demandes de `.php` pages. Pour ce faire, ouvrez la `setup_web.cmd` fichier (dans le `bin` fichier du répertoire racine de votre rôle web) dans un éditeur de texte et remplacez son contenu par le script suivant :

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Ajouter des fichiers de votre application au répertoire racine de votre rôle de serveur web. Il s’agit répertoire racine du serveur web.

6. Publier votre application comme décrit dans la section [publier votre application](#how-to-publish-your-application) ci-dessous.

> [AZURE.NOTE] La `download.ps1` script (dans le `bin` dossier du répertoire racine le rôle de serveur web) peut être supprimé après avoir suivi les étapes ci-dessus pour l’utilisation de votre propre runtime PHP.

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurer un rôle de collaborateur pour utiliser votre propre PHP d’exécution

Pour configurer un rôle de collaborateur pour utiliser un runtime PHP que vous fournissez, procédez comme suit :

1. Créer un projet de Service Azure et ajoutez un rôle de collaborateur PHP comme décrit plus haut dans cette rubrique.
2. Créer un `php` dossier dans le répertoire racine du rôle de collaborateur, puis ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) à la `php` dossier.
3. (FACULTATIF) Si votre runtime PHP utilise des [Pilotes de Microsoft pour PHP pour SQL Server][sqlsrv drivers], vous devrez configurer votre rôle de collaborateur pour installer [SQL Server Native Client 2012] [ sql native client] lorsqu’il est mis en service. Pour ce faire, ajoutez [programme d’installation sqlncli.msi x64] au répertoire racine du rôle de collaborateur. Le script de démarrage décrit dans l’étape suivante s’exécutera automatiquement le programme d’installation lorsque le rôle est mis en service. Si votre runtime PHP n’utilise pas le Microsoft Drivers pour PHP pour SQL Server, vous pouvez supprimer la ligne suivante à partir du script indiqué dans l’étape suivante :

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Définir une tâche de démarrage qui ajoute votre `php.exe` exécutable à variable d’environnement PATH du rôle Collaborateur lorsque le rôle est mis en service. Pour ce faire, ouvrez la `setup_worker.cmd` de fichier (dans le répertoire racine du rôle de collaborateur) dans un éditeur de texte et remplacez son contenu par le script suivant :

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. Ajouter des fichiers de votre application au répertoire racine de votre rôle de travail.

6. Publier votre application comme décrit dans la section [publier votre application](#how-to-publish-your-application) ci-dessous.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Exécutez l’application dans les émulateurs informatiques et de stockage

Les émulateurs Azure proposent un environnement local dans lequel vous pouvez tester votre application Azure avant de déployer dans le cloud. Il existe certaines différences entre les émulateurs et l’environnement Azure. Pour mieux comprendre ce point, voir [utiliser l’émulateur de stockage Azure de développement et de test](./storage/storage-use-emulator.md).

Notez que vous devez disposer PHP installé localement pour utiliser l’émulateur de calcul. L’émulateur cluster utilisera votre installation PHP locale pour exécuter votre application.

Pour exécuter votre projet dans les émulateurs, exécutez la commande suivante à partir du répertoire racine de votre projet :

    PS C:\MyProject> Start-AzureEmulator

Vous verrez ressemblant à ceci :

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Vous pouvez voir votre application en cours d’exécution dans l’émulateur en ouvrant un navigateur web et en accédant à l’adresse locale figurant dans le résultat (`http://127.0.0.1:81` dans l’exemple ci-dessus).

Pour arrêter les émulateurs, exécutez la commande suivante :

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publiez votre application

Pour publier votre application, vous devez tout d’abord importer vos paramètres de publication à l’aide de l’applet de commande [Importer AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) . Ensuite, vous pouvez publier votre application à l’aide de l’applet de commande [Publier AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Pour plus d’informations sur la connexion, voir [comment installer et configurer Azure PowerShell](powershell-install-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement PHP](/develop/php/).

[Azure SDK pour PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[définition du service (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuration du service (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[programme d’installation SQLNCLI.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
