<properties
    pageTitle="Application web application Service Azure config et extensions avancées"
    description="Utiliser les déclarations Transformation(XDT) de Document XML pour transformer le fichier ApplicationHost.config dans votre application web Azure Application Service et ajouter des extensions privées pour activer les actions d’administration personnalisé."
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Application web application Service Azure config et extensions avancées

À l’aide de déclarations de [Transformation de Document XML](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), vous pouvez transformer le fichier [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) dans votre application web dans le Service d’application Azure. Vous pouvez également utiliser les déclarations XDT pour ajouter des extensions privées pour activer les actions de l’administration d’application web personnalisée. Cet article inclut un exemple gestionnaire PHP web app d’extension qui permet de gérer des paramètres PHP via une interface web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configuration avancée via ApplicationHost.config
La plate-forme application Service offre souplesse et contrôle pour la configuration d’application web. Bien que le fichier de configuration IIS ApplicationHost.config standard n’est pas disponible pour modification directe dans le Service d’application, la plateforme prend en charge un modèle de transformation ApplicationHost.config déclaratif basé sur XML Document Transformation (XDT).

Pour tirer parti de cette fonctionnalité de transformation, vous créez un fichier ApplicationHost.xdt avec contenu XDT et place sous la racine du site (d:\home\site) dans la [Console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). Vous devrez peut-être redémarrer l’application Web pour les modifications soient prises en compte.

L’exemple applicationHost.xdt suivant montre comment ajouter une nouvelle variable d’environnement personnalisés pour une application web qui utilise PHP 5.4.

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


Un fichier journal avec statut de la transformation et des détails est disponible depuis la racine FTP sous LogFiles\Transform.

Pour plus d’exemples, voir [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Remarque**<br />
Éléments à partir de la liste des modules sous `system.webServer` ne peut pas être supprimé ou réapprovisionnés, mais ajouts à la liste sont possibles.


##<a id="extend"></a>Étendre votre application web

###<a id="overview"></a>Vue d’ensemble des extensions de l’application web privé

Service d’application prend en charge les extensions de l’application web comme un point d’extensibilité pour les actions d’administration. En fait, certaines fonctionnalités de plateforme de Service d’application sont implémentées comme des extensions préinstallées. Tandis que les extensions de plateforme préinstallé ne peut pas être modifiées, vous pouvez créer et configurer des extensions privées pour votre propre application web. Cette fonctionnalité s’appuie également sur les déclarations XDT. Les principales étapes de création d’une extension de l’application web privés sont les suivantes :

1. Application extension **contenu**Web : créer une application web pris en charge par le Service d’application
2. Web application extension **déclaration**: créer un fichier ApplicationHost.xdt
3. Application extension **déploiement**Web : placer du contenu dans le dossier SiteExtensions sous`root`

Liens internes pour l’application web doivent pointer vers un chemin d’accès par rapport au chemin d’accès de l’application spécifié dans le fichier ApplicationHost.xdt. Toute modification apportée au fichier ApplicationHost.xdt exige une Corbeille de l’application web.

**Remarque**: informations supplémentaires pour ces éléments clés sont disponibles dans [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Un exemple détaillé est inclus pour illustrer les étapes de création et l’activation d’une extension de l’application web privé. Le code source de l’exemple de gestionnaire PHP qui suit peut être téléchargé à partir de [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a>Exemple d’extension Web app : gestionnaire PHP

Gestionnaire PHP est une extension de l’application web qui permet aux administrateurs d’application afficher et configurer leurs paramètres PHP à l’aide d’une interface web au lieu de devoir modifier directement des fichiers .ini PHP facilement de web. Fichiers de configuration courants pour PHP incluent le fichier php.ini situé sous Program Files et. user.ini fichier situé dans le dossier racine de votre application web. Dans la mesure où le fichier php.ini n’est pas directement modifiable sur la plateforme de Service d’application, l’extension gestionnaire PHP utilise la. fichier user.ini pour appliquer les modifications du paramétrage.

####<a id="PHPwebapp"></a>L’application web PHP Manager

Voici la page d’accueil du déploiement gestionnaire PHP :

![TransformSitePHPUI][TransformSitePHPUI]

Comme vous pouvez le voir, une extension de l’application web est comme une application web régulière, mais avec un autre fichier ApplicationHost.xdt placé dans le dossier racine de l’application web (plus de détails sur le fichier ApplicationHost.xdt sont disponibles dans la section suivante de cet article).

L’extension du Gestionnaire de PHP a été créée en utilisant le modèle Visual Studio ASP.NET MVC 4 Web Application. La vue suivante à partir de l’Explorateur de solutions présente la structure de l’extension du Gestionnaire de PHP.

![TransformSiteSolEx][TransformSiteSolEx]

La logique spéciale uniquement nécessaire pour le fichier e/s consiste à indiquer l’endroit où se trouve le répertoire wwwroot de l’application web. Comme indiqué dans l’exemple suivant, la variable environnement « HOME » indique le chemin d’accès de la racine de l’application web et le chemin d’accès wwwroot peut être construit en ajoutant « site\wwwroot » :

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


Une fois que vous avez le chemin d’accès, vous pouvez utiliser les opérations e/s fichier normal pour lire et écrire sur des fichiers.

Un point de faire preuve de prudence avec les extensions de l’application web ce qui concerne la gestion des liens internes.  Si vous avez tous les liens dans vos fichiers HTML qui donnent des chemins d’accès absolus pour les liens internes dans votre application web, vous devez vérifier que ces liens sont précédées de votre nom d’extension en tant que votre racine. Cela est nécessaire car la racine de votre numéro de poste est désormais « /`[your-extension-name]`/ « plutôt que d’être simples « / », donc tout internes liens doivent être mis à jour en conséquence. Par exemple, supposons que votre code inclut un lien vers les éléments suivants :

`"<a href="/Home/Settings">PHP Settings</a>"`

Lorsque le lien fait partie d’une extension de l’application web, le lien doit être placé dans l’écran suivant :

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Vous pouvez contourner cette exigence en des options à l’aide des seuls les chemins relatifs au sein de votre application web, ou dans le cas d’applications ASP.NET, à l’aide de la `@Html.ActionLink` méthode qui crée les liens appropriés pour vous.

####<a id="XDT"></a>Le fichier applicationHost.xdt

Le code pour votre numéro de poste de l’application web est inséré sous %HOME%\SiteExtensions\[votre nom de l’extension]. Nous allons appeler cette la racine de l’extension.  

Pour enregistrer votre numéro de poste de l’application web avec le fichier applicationHost.config, vous devez placer un fichier nommé ApplicationHost.xdt à la racine de l’extension. Le contenu du fichier ApplicationHost.xdt doit être comme suit :

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

Le nom que vous avez sélectionné comme votre nom d’extension doit porter le même nom que votre dossier racine extension.

Cela a pour effet de l’ajout d’un nouveau chemin d’application à la `system.applicationHost` liste des sites sous le site SCM. Le site SCM est un point de fin d’administration site avec des droits d’accès spécifiques. L’URL est `https://[your-site-name].scm.azurewebsites.net`.  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Déploiement d’une extension Web app

Pour installer votre numéro de poste de l’application web, vous pouvez utiliser FTP pour copier tous les fichiers de votre application web à la `\SiteExtensions\[your-extension-name]` dossier de l’application web sur lequel vous voulez installer l’extension.  Veillez à copier le fichier ApplicationHost.xdt à cet emplacement également. Redémarrez votre application web pour activer l’extension.

Vous devez être en mesure de voir votre numéro de poste de l’application web en :

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Notez que l’URL ressemble à l’URL de votre application web, excepté qu’elle utilise le protocole HTTPS et contient « .scm ».

Il est possible de désactiver toutes les extensions (pas préinstallées) privées pour votre application web lors du développement et investigations en ajoutant des paramètres de l’application avec la touche `WEBSITE_PRIVATE_EXTENSIONS` et une valeur de `0`.

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
