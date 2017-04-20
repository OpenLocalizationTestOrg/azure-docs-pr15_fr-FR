<properties
   pageTitle="Utilisation de Scripts Windows PowerShell pour publier au développement et aux environnements de Test | Microsoft Azure"
   description="Découvrez comment utiliser les scripts Windows PowerShell à partir de Visual Studio publier au développement et environnements de test."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>L’utilisation de scripts Windows PowerShell pour publier sur le développement et environnements de test

Lorsque vous créez une application web dans Visual Studio, vous pouvez générer un script Windows PowerShell que vous pouvez utiliser ultérieurement pour automatiser la publication de votre site Web pour Azure comme une application Web dans le Service d’application Azure ou une machine virtuelle. Vous pouvez modifier et étendre le script Windows PowerShell dans l’éditeur Visual Studio pour l’adapter à vos besoins ou intégrer le script build existante, de test et les scripts de publication.

À l’aide de ces scripts, vous pouvez prévoir des versions personnalisées (également appelé environnements de test et de développement) de votre site pour une utilisation temporaire. Par exemple, vous pouvez configurer une version particulière de votre site Web sur une machine virtuelle Azure ou sur l’emplacement intermédiaire sur un site Web pour exécuter une suite de tests, reproduire un bogue, testez un correctif bogue, version d’évaluation d’une proposition de modification ou configurer un environnement personnalisé pour une démonstration ou une présentation. Une fois que vous avez créé un script qui publie votre projet, vous pouvez recréer les environnements identiques en exécutant le script de nouveau selon vos besoins, ou exécuter le script avec votre propre génération de votre application web pour créer un environnement personnalisé pour le test.

## <a name="what-you-need"></a>Vous avez besoin

- Kit de développement Azure 2.3 ou version ultérieure. Pour plus d’informations, voir [Téléchargements de Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384) .

Vous n’avez pas besoin du SDK Azure pour générer les scripts pour les projets web. Cette fonctionnalité est pour les projets web, pas les rôles web dans les services en nuage.

- Azure PowerShell 0.7.4 ou version ultérieure. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](powershell-install-configure.md) .

- [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) ou version ultérieure.

## <a name="additional-tools"></a>Outils supplémentaires

Ressources pour l’utilisation de PowerShell dans Visual Studio pour le développement d’Azure et des outils supplémentaires sont disponibles. Consultez [PowerShell Tools pour Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Générer les scripts de publication

Vous pouvez générer les scripts de publication pour une machine virtuelle qui héberge votre site Web lorsque vous créez un nouveau projet en suivant [les instructions ci-après](./virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md). Vous pouvez également [Générer publier des scripts pour les applications web dans le Service d’application Azure](./app-service-web/web-sites-dotnet-get-started.md).

## <a name="scripts-that-visual-studio-generates"></a>Scripts que Visual Studio génère

Visual Studio génère un dossier de niveau de solution nommé **PublishScripts** qui contient deux fichiers Windows PowerShell, un script de publication pour votre ordinateur virtuel ou le site Web et un module qui contient des fonctions que vous pouvez utiliser dans les scripts. Visual Studio génère également un fichier au format JSON qui spécifie les détails du projet que vous déployez.

### <a name="windows-powershell-publish-script"></a>Windows PowerShell publier script

Le script publier contient des étapes publier spécifiques pour le déploiement sur un site Web ou de la machine virtuelle. Visual Studio fournit la syntaxe de la couleur pour le développement de Windows PowerShell. L’aide pour les fonctions est disponible, et vous pouvez modifier librement les fonctions dans le script pour l’adapter à vos besoins en matière de modifications.

### <a name="windows-powershell-module"></a>Module Windows PowerShell

Le module Windows PowerShell que Visual Studio génère contient des fonctions qui utilise le script publier. Ils sont des fonctions PowerShell Azure et ne sont pas destinés à être modifiée. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](powershell-install-configure.md) .

### <a name="json-configuration-file"></a>Fichier de configuration JSON

Le fichier JSON est créé dans le dossier **Configurations** et contient les données de configuration qui spécifie exactement les ressources à déployer sur Azure. Le nom du fichier que Visual Studio génère est projet-nom-WAWS-dev.json si vous avez créé un site Web ou projet nom-machine virtuelle-dev.json si vous avez créé une machine virtuelle. Voici un exemple de fichier de configuration JSON est généré lorsque vous créez un site Web. La plupart des valeurs est explicite. Le nom du site Web est généré par Azure, afin qu’il peut correspondent pas à votre nom de projet.

```
{
"environmentSettings": {
"webSite": {
"name": "WebApplication26632",
"location": "West US"
},
"databases": [
{
"connectionStringName": "DefaultConnection",
"databaseName": "WebApplication26632_db",
"serverName": "YourDatabaseServerName",
"user": "sqluser2",
"password": "",
"edition": "",
"size": "",
"collation": "",
"location": "West US"
}
]
}
}
```
Lorsque vous créez une machine virtuelle, le fichier de configuration JSON est semblable à ce qui suit. Notez qu’un service cloud est créé comme un conteneur pour la machine virtuelle. La machine virtuelle contient les points de terminaison habituels pour accéder au web via HTTP et HTTPS, ainsi que les points de terminaison pour déployer Web, qui vous permet de publier sur le site Web à partir de votre ordinateur local, Bureau à distance et Windows PowerShell.

```
{
"environmentSettings": {
"cloudService": {
"name": "myusernamevm1",
"affinityGroup": "",
"location": "West US",
"virtualNetwork": "",
"subnet": "",
"availabilitySet": "",
"virtualMachine": {
"name": "myusernamevm1",
"vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
"size": "Small",
"user": "vmuser1",
"password": "",
"enableWebDeployExtension": true,
"endpoints": [
{
"name": "Http",
"protocol": "TCP",
"publicPort": "80",
"privatePort": "80"
},
{
"name": "Https",
"protocol": "TCP",
"publicPort": "443",
"privatePort": "443"
},
{
"name": "WebDeploy",
"protocol": "TCP",
"publicPort": "8172",
"privatePort": "8172"
},
{
"name": "Remote Desktop",
"protocol": "TCP",
"publicPort": "3389",
"privatePort": "3389"
},
{
"name": "Powershell",
"protocol": "TCP",
"publicPort": "5986",
"privatePort": "5986"
}
]
}
},
"databases": [
{
"connectionStringName": "",
"databaseName": "",
"serverName": "",
"user": "",
"password": ""
}
],
"webDeployParameters": {
"iisWebApplicationName": "Default Web Site"
}
}
}
```

Vous pouvez modifier la configuration JSON pour modifier ce qui se passe lorsque vous exécutez les scripts publier. La `cloudService` et `virtualMachine` sections sont obligatoires, mais vous pouvez supprimer la `databases` section si vous n’avez pas besoin. Les propriétés qui sont vides dans le fichier de configuration par défaut que Visual Studio génère sont facultatives ; ceux qui ont des valeurs dans le fichier de configuration par défaut sont requis.

Si vous avez un site Web qui comporte plusieurs environnements de déploiement (appelés emplacements) au lieu d’un site de production unique dans Azure, vous pouvez inclure le nom de l’emplacement dans le nom du site Web dans le fichier de configuration JSON. Par exemple, si vous avez un site Web qui a nommé **monsite** et un emplacement pour qu’elle nommé **tester** puis l’URI est test.cloudapp.net de sites Mon site, mais le nom correct à utiliser dans le fichier de configuration est mysite(test). Vous pouvez seulement effectuer cette option si le site Web et emplacements existent déjà dans votre abonnement. S’ils n’existent, créez le site Web en exécutant le script sans spécifier l’emplacement, puis créer l’emplacement dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885)et par la suite exécuter le script avec le nom du site Web modifié. Pour plus d’informations sur les emplacements de déploiement pour les applications web, voir [configurer la mise en environnements pour les applications web dans le Service d’application Azure](./app-service-web/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Comment faire pour exécuter les scripts de publication

Si vous n’avez jamais exécuté un script Windows PowerShell avant, vous devez commencer par définir la stratégie d’exécution pour permettre l’exécution de scripts. Il s’agit d’une fonctionnalité de sécurité pour empêcher les utilisateurs d’exécuter des scripts Windows PowerShell s’ils sont concernés par les programmes malveillants ou virus qui impliquent l’exécution de scripts.

### <a name="run-the-script"></a>Exécuter le script

1. Créez le package de déploiement Web pour votre projet. Un package de déploiement Web est une archive compressée (fichier .zip) qui contiennent les fichiers que vous souhaitez copier vers votre site Web ou une machine virtuelle. Vous pouvez créer des packages de déploiement Web dans Visual Studio pour n’importe quelle application web.

![Créer des Web déployer le Package](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Pour plus d’informations, voir [Comment : créer un Package de déploiement Web dans Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Vous pouvez également automatiser la création de votre package de déploiement Web, comme décrit dans la section **personnalisation et étendre les scripts publier** plus loin dans cette rubrique.

1. Dans l' **Explorateur de solutions**, ouvrez le menu contextuel pour le script et puis sélectionnez **Ouvrir avec PowerShell ISE**.

1. Si c’est la première fois que vous avez exécuté de scripts Windows PowerShell sur cet ordinateur, ouvrez une fenêtre d’invite de commandes en tant qu’administrateur et tapez la commande suivante :

`Set-ExecutionPolicy RemoteSigned`

1. Connectez-vous à Azure à l’aide de la commande suivante.

`Add-AzureAccount`

Lorsque vous y êtes invité, fournissez votre nom d’utilisateur et mot de passe.

Notez que cette méthode de fournir des informations d’identification Azure ne fonctionne pas lorsque vous automatisez le script. À la place, vous devez utiliser le fichier .publishsettings pour fournir des informations d’identification. Une fois seulement, vous utilisez la commande **Get-AzurePublishSettingsFile** à télécharger le fichier à partir d’Azure et par la suite permet **d’Importer AzurePublishSettingsFile** pour importer le fichier. Pour obtenir des instructions détaillées, voir [comment installer et configurer Azure PowerShell](powershell-install-configure.md).

1. (Facultatif) Si vous voulez créer des ressources Azure tels que la machine virtuelle, la base de données et le site Web sans publication de votre application web, utilisez la commande **Publier WebApplication.ps1** avec la **-Configuration** argument défini sur le fichier de configuration JSON. Cette ligne de commande utilise le fichier de configuration JSON pour déterminer les ressources à créer. Comme il utilise les paramètres par défaut pour les autres arguments de ligne de commande, il crée les ressources, mais ne publiez votre application web. – Commentaires option vous donne des informations sur ce qui se passe.

`Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. Utilisez la commande **Publier WebApplication.ps1** comme indiqué dans un des exemples suivants pour appeler le script et publier votre application web. Si vous avez besoin remplacer les paramètres par défaut pour tous les autres arguments, telles que le nom de l’abonnement, publier le nom du package, les informations d’identification de la machine virtuelle ou les informations d’identification du serveur de base de données, vous pouvez spécifier les paramètres. Utiliser la **– commentaires** option pour afficher plus d’informations sur l’avancement de la publication.

```
Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
–SubscriptionName Contoso `
-WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
-DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
-Verbose
```

Si vous créez une machine virtuelle, la commande ressemble à ceci. Cet exemple montre également comment spécifier les informations d’identification pour plusieurs bases de données. Pour les ordinateurs virtuels ils créent, le certificat SSL n’est pas à partir d’une autorité de certification racine de confiance. Par conséquent, vous devez utiliser l’option **– AllowUntrusted** .

```
Publish-WebApplication.ps1 `
-Configuration C:\Path\ADVM-VM-test.json `
-SubscriptionName Contoso `
-WebDeployPackage C:\Path\ADVM.zip `
-AllowUntrusted `
-VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
-DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
-Verbose
```

Le script peut créer des bases de données, mais ne les serveurs de base de données. Si vous souhaitez créer un serveur de base de données, vous pouvez utiliser la fonction **New-AzureSqlDatabaseServer** dans le module Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Personnaliser et étendre les scripts de publication

Vous pouvez personnaliser le script publier et le fichier de configuration JSON. Les fonctions dans le module Windows PowerShell **AzureWebAppPublishModule.psm1** ne sont pas destinées à être modifiée. Si vous voulez simplement spécifier une autre base de données ou modifier certaines des propriétés de la machine virtuelle, modifiez le fichier de configuration JSON. Si vous souhaitez étendre les fonctionnalités du script pour automatiser la création et test de votre projet, vous pouvez implémenter stub de fonction dans **Publier WebApplication.ps1**.

Pour automatiser la création de votre projet, ajoutez le code qui appelle MSBuild pour `New-WebDeployPackage` comme le montre cet exemple de code. Le chemin d’accès à la commande MSBuild est différent en fonction de la version de Visual Studio que vous avez installé. Pour obtenir le chemin d’accès correct, vous pouvez utiliser la fonction **Get-MSBuildCmd**, comme illustré dans cet exemple.

### <a name="to-automate-building-your-project"></a>Pour automatiser la création de votre projet

1. Ajouter la `$ProjectFile` paramètre dans la section paramètre global.

```
[Parameter(Mandatory = $false)]
  [ValidateScript({Test-Path $_ -PathType Leaf})]
  [String]
  $ProjectFile,
```

1. Copiez la fonction `Get-MSBuildCmd` dans votre fichier de script.

```
function Get-MSBuildCmd
{
        process
{

             $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                   Sort-Object {[double]$_.PSChildName} -Descending |
                                   Select-Object -First 1 |
                                   Get-ItemProperty -Name MSBuildToolsPath |
                                   Select -ExpandProperty MSBuildToolsPath
       
            $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

        return Get-Item $path
    }
}
```

1. Remplacer `New-WebDeployPackage` avec le code suivant et remplacez les espaces réservés dans la ligne construire `$msbuildCmd`. Ce code est Visual Studio 2015. Si vous utilisez Visual Studio 2013, modifiez la propriété **VisualStudioVersion** ci-dessous pour `12.0`.

```
function New-WebDeployPackage
{
    #Write a function to build and package your web application
      
#To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
      
Write-VerboseWithTime 'Build-WebDeployPackage: Start'
      
$msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
      
Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
      
#Start execution of the build command
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
      
if ($job.ExitCode -ne 0)
{
throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName
      
#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
      
      
#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
      
Write-VerboseWithTime 'Build-WebDeployPackage: End'
      
return $WebDeployPackage
}
```

1. Appelez le `New-WebDeployPackage` fonction avant cette ligne : `$Config = Read-ConfigFile $Configuration` pour les applications web ou `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` pour les machines virtuelles.

```
if($ProjectFile)
{
$WebDeployPackage = New-WebDeployPackage
}
```

1. Appeler le script personnalisé à partir de la ligne de commande utilisant le passage de la `$Project` argument, comme dans la ligne de commande exemple suivante.

```
.\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
-ProjectFile ..\WebApplication5\WebApplication5.csproj `
-VMPassword @{Name="VMUser";Password="Test.123"} `
-AllowUntrusted `
-Verbose
```

Pour automatiser les tests de votre application, ajoutez le code à `Test-WebApplication`. Veillez à ne pas commenter les lignes de **Publier WebApplication.ps1** où ces fonctions sont appelées. Si vous ne fournissez une implémentation, vous pouvez créer manuellement votre projet avec Visual Studio et puis exécuter le script publier pour publier sur Azure.

## <a name="publishing-function-summary"></a>Synthèse des fonctions publication

Pour obtenir de l’aide pour les fonctions que vous pouvez utiliser à l’invite de commande Windows PowerShell, utilisez la commande `Get-Help function-name`. L’aide comprend des exemples et aide sur le paramètre. Le texte d’aide même est également dans les fichiers de sources de script, **AzureWebAppPublishModule.psm1** et **WebApplication.ps1 de publier**. Le script et l’aide est localisées dans la langue de Visual Studio.

**AzureWebAppPublishModule**

|Nom de la fonction|Description|
|---|---|
|AzureSQLDatabase ajouter|Crée une nouvelle base de données SQL Azure.|
|AzureSQLDatabases ajouter|Crée des bases de données SQL Azure à partir des valeurs dans le fichier de configuration : JSON que Visual Studio génère.|
|AzureVM ajouter|Crée une machine virtuelle Azure et renvoie l’URL de la machine virtuelle déployée. La fonction configure les conditions préalables, puis appelle la fonction **New-AzureVM** (module Azure) pour créer un nouvel ordinateur virtuel.|
|AzureVMEndpoints ajouter|Ajoute de nouveaux points de terminaison d’entrée à une machine virtuelle et renvoie la machine virtuelle avec le nouveau point de terminaison.|
|AzureVMStorage ajouter|Crée un nouveau compte de stockage Azure dans l’abonnement actif. Le nom du compte commence par « devtest » suivi d’une chaîne alphanumérique unique. La fonction renvoie le nom du nouveau compte de stockage. Vous devez spécifier un emplacement ou un groupe affinité pour le nouveau compte de stockage.|
|AzureWebsite ajouter|Crée un site Web avec le nom spécifié et l’emplacement. Cette fonction appelle la fonction **New-AzureWebsite** dans le module Azure. Si l’abonnement n’inclut pas déjà un site Web avec le nom spécifié, cette fonction crée le site Web et renvoie un objet de site Web. Sinon, elle retourne `$null`.|
|Abonnement à la sauvegarde|Enregistre l’abonnement Azure active dans le `$Script:originalSubscription` variable dans la portée de script. Cette fonction enregistre l’abonnement Azure active (tels qu’obtenus à `Get-AzureSubscription -Current`) et son compte de stockage et l’abonnement est modifié par ce script (stockée dans la variable `$UserSpecifiedSubscription`) et le compte de stockage, dans la portée de script. En enregistrant les valeurs, vous pouvez utiliser une fonction, tel que `Restore-Subscription`, pour restaurer le compte d’abonnement et le stockage actuel d’origine à l’état actuel si l’état actuel a changé.|
|Rechercher AzureVM|Obtient la machine virtuelle Azure spécifiée.|
|Format DevTestMessageWithTime|Insère la date et l’heure à un message. Cette fonction est destinée aux messages enregistrés dans les flux d’erreur et les commentaires.|
|Get-AzureSQLDatabaseConnectionString|Assemble une chaîne de connexion pour vous connecter à une base de données SQL Azure.|
|Get-AzureVMStorage|Renvoie le nom du premier compte de stockage sur le modèle de nom « devtest*» (majuscules ou minuscules) dans l’emplacement spécifié ou du groupe affinité. Si la « devtest*» compte de stockage ne correspondent pas à l’emplacement ou au groupe affinité, la fonction ignore. Vous devez spécifier un emplacement ou un groupe affinité.|
|Get-MSDeployCmd|Renvoie une commande pour exécuter l’outil MsDeploy.exe.|
|Nouvelle AzureVMEnvironment|Recherche ou crée une machine virtuelle dans l’abonnement qui correspondent aux valeurs dans le fichier de configuration JSON.|
|WebPackage publier|Utilisations MsDeploy.exe et un site web publient le package. Fichier zip à déployer des ressources à un site Web. Cette fonction ne génère pas de sortie. Si l’appel à MSDeploy.exe échoue, la fonction lève une exception. Pour obtenir la sortie plus détaillée, utilisez la **-détaillée** option.|
|WebPackageToVM publier|Vérifie les valeurs de paramètre et appelle ensuite la fonction **Publier WebPackage** .|
|Lecture ConfigFile|Valide le fichier de configuration JSON et retourne une table de hachage des valeurs sélectionnées.|
|Restaurer abonnement|Réinitialise l’abonnement en cours à l’abonnement d’origine.|
|Test-AzureModule|Renvoie `$true` si la version du module Azure installé est 0.7.4 ou version ultérieure. Renvoie `$false` si le module n’est pas installé ou une version antérieure. Cette fonction n’a pas de paramètres.|
|Test-AzureModuleVersion|Renvoie `$true` si la version du module Azure est 0.7.4 ou version ultérieure. Renvoie `$false` si le module n’est pas installé ou une version antérieure. Cette fonction n’a pas de paramètres.|
|Test-HttpsUrl|Convertit l’URL saisie à un objet System.Uri. Renvoie `$True` si l’URL est absolue et son modèle est https. Renvoie `$false` si l’URL est relative, son schéma n’est pas HTTPS ou la chaîne d’entrée ne peut pas être convertie en une URL.|
|Membre de test|Renvoie `$true` si une propriété ou méthode est un membre de l’objet. Sinon, retourne `$false`.|
|Écriture ErrorWithTime|Écrit un message d’erreur le préfixe à l’heure actuelle. Cette fonction appelle la fonction **Format DevTestMessageWithTime** pour ajouter l’heure avant d’écrire le message dans le flux d’erreurs.|
|Écriture HostWithTime|Écrit un message dans le programme hôte (**Écriture hôte**) le préfixe à l’heure actuelle. L’effet d’écriture dans le programme hôte varie. La plupart des programmes hébergeant Windows PowerShell écrire ces messages vers la sortie standard.|
|Écriture VerboseWithTime|Écrit un message détaillé précédé avec l’heure actuelle. Parce qu’il appelle **Écriture commentaires**, le message s’affiche uniquement lorsque le script s’exécute avec le paramètre de **commentaires** ou lorsque la préférence **VerbosePreference** est définie sur **Continue**.|

**Application Web publier**

|Nom de la fonction|Description|
|---|---|
|Nouvelle AzureWebApplicationEnvironment|Crée des ressources Azure, tel qu’un site Web ou de la machine virtuelle.|
|Nouvelle WebDeployPackage|Cette fonction n’est pas activée. Vous pouvez ajouter des commandes dans cette fonction pour générer votre projet.|
|AzureWebApplication publier|Publie une application web vers Azure.|
|Application Web publier|Crée et déploie Web Apps, des machines virtuelles, des bases de données SQL et des comptes de stockage d’un projet web Visual Studio.|
|Application Web test|Cette fonction n’est pas activée. Vous pouvez ajouter des commandes dans cette fonction pour tester votre application.|

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’écriture de script PowerShell en lisant [l’écriture de scripts avec Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) et voir les autres scripts PowerShell Azure à partir du [Centre de scripts](https://azure.microsoft.com/documentation/scripts/).
