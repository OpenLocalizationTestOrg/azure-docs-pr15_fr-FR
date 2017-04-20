<properties
    pageTitle="Remise continue pour le cloud services avec TFS dans Azure | Microsoft Azure"
    description="Découvrez comment configurer la remise continue pour les applications cloud Azure. Exemples de code pour les instructions de ligne de commande MSBuild et des scripts PowerShell."
    services="cloud-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/30/2016"
    ms.author="tarcher"/>

# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Remise continue pour les Services de Cloud dans Azure

La procédure décrite dans cet article vous montre comment configurer la remise continue pour les applications cloud Azure. Ce processus vous permet de créer automatiquement des packages et déployer le package vers Azure après l’archivage de chaque code. Le processus de génération de package décrit dans cet article équivaut à la commande **Package** dans Visual Studio, et la procédure de publication est équivalente à la commande **Publier** dans Visual Studio.
L’article aborde les méthodes que vous utiliseriez pour créer un serveur de génération avec MSBuild d’instructions de ligne de commande et de scripts Windows PowerShell, et il montre comment vous pouvez également configurer Visual Studio Team Foundation Server - définitions de Team Build à utiliser les commandes de MSBuild et les scripts PowerShell. Le processus est personnalisable pour votre environnement de génération et des environnements Azure cible.

Vous pouvez également utiliser Visual Studio Team Services, une version de TFS est hébergé dans Azure, pour effectuer cette action plus facilement. Pour plus d’informations, voir [Remise continue à Azure par à l’aide de Visual Studio Team Services][].

Avant de commencer, vous devez publier votre application à partir de Visual Studio.
Cela garantit que toutes les ressources sont disponibles et initialisé lorsque vous essayez d’automatiser le processus de publication.

## <a name="1-configure-the-build-server"></a>1 : configurer le serveur de génération

Avant de pouvoir créer un package d’Azure à l’aide de MSBuild, vous devez installer les outils et les logiciels nécessaires sur le serveur de génération.

Visual Studio n’est pas doit être installée sur le serveur de génération. Si vous voulez utiliser le Service Team Foundation Build pour gérer votre serveur de génération, suivez les instructions dans la documentation de [Service Team Foundation Build][] .

1.  Sur le serveur de génération, installez le [.NET Framework 4.5.2][], qui inclut MSBuild.
2.  Installer les [Outils de création de Azure pour .NET](https://azure.microsoft.com/develop/net/)plus récents.
3.  Installer les [bibliothèques Azure pour .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4.  Copiez le fichier Microsoft.WebApplication.targets à partir d’une installation Visual Studio sur le serveur de génération.

    Sur un ordinateur avec Visual Studio installé, ce fichier se trouve dans le répertoire C:\\programme Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Vous devez le copier dans le même répertoire sur le serveur de génération.
5.  Installez les [Outils Azure pour Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2 : créer un Package à l’aide des commandes MSBuild

Cette section décrit comment construire une commande MSBuild qui crée un package d’Azure. Exécuter cette étape sur le serveur de génération pour vérifier que tout est correctement configuré et que la commande MSBuild signifie que vous souhaitez qu’il fasse. Vous pouvez ajouter cette ligne de commande à script de compilation existant sur le serveur de génération, ou vous pouvez utiliser la ligne de commande dans une définition de Build TFS, comme décrit dans la section suivante. Pour plus d’informations sur les paramètres de ligne de commande et MSBuild, voir [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1.  Si Visual Studio est installé sur le serveur de génération, repérez et sélectionnez **Invite de commande Visual Studio** dans le dossier **Outils Visual Studio** dans Windows.

    Si Visual Studio n’est pas installé sur le serveur de création, ouvrez une invite de commandes et vérifiez que MSBuild.exe est accessible sous le chemin d’accès. MSBuild est installé avec .NET Framework dans le dossier % de chemin d’accès\\Microsoft.NET\\Framework\\*Version*. Par exemple, pour ajouter MSBuild.exe à la variable d’environnement PATH lorsque vous avez .NET Framework 4 est installé, tapez la commande suivante à l’invite :

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  À l’invite de commandes, accédez au dossier contenant le fichier de projet Azure que vous voulez créer.

3.  Exécution avec l’option /target : publier option comme dans l’exemple suivant :

        MSBuild /target:Publish

    Cette option peut être abrégée sous /t : publier. L’option /t:Publish dans MSBuild ne doit pas être confondue avec les commandes de publication disponibles dans Visual Studio lorsque vous avez installé le kit SDK Azure. La /t : publier des packages Azure générations seule option. Il ne déploie pas les packages comme les commandes publier dans Visual Studio.

    Si vous le souhaitez, vous pouvez spécifier le nom du projet en tant que MSBuild paramètre. Le cas contraire, le répertoire actuel est utilisé. Pour plus d’informations sur les options de ligne de commande MSBuild, voir [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

4.  Recherchez la sortie. Par défaut, cette commande crée un répertoire en relation avec le dossier racine pour le projet, tels que *ProjectDir*\\emplacement\\*Configuration*\\app.publish\\. Quand vous créez un projet d’Azure, vous générez deux fichiers, le fichier de package et le fichier de configuration associées :

    -   Project.cspkg
    -   ServiceConfiguration. *TargetProfile*.cscfg

    Par défaut, chaque projet Azure comprend un fichier de configuration de service (fichier .cscfg) pour les versions (débogage) locales et une autre pour les versions cloud (intermédiaire ou production), mais vous pouvez ajouter ou supprimer des fichiers de configuration de service selon vos besoins. Quand vous créez un package dans Visual Studio, vous êtes invité le fichier de configuration de service à inclure à côté du package.

5.  Spécifiez le fichier de configuration de service. Quand vous créez un package à l’aide de MSBuild, le fichier de configuration du service local est inclus par défaut. Pour inclure un fichier de configuration de services différente, définissez la propriété TargetProfile de la commande MSBuild, comme dans l’exemple suivant :

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Spécifier un emplacement pour la sortie. Définir le chemin d’accès à l’aide de la /p:PublishDir =*répertoire* \\ option, y compris le séparateur à droite d’une barre oblique inverse, comme dans l’exemple suivant :

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Une fois que vous avez construit et testé une ligne de commande MSBuild appropriés pour créer vos projets et les combiner dans un package d’Azure, vous pouvez ajouter cette ligne de commande à vos scripts de compilation. Si votre serveur de génération utilise des scripts personnalisés, ce processus varient selon les caractéristiques de votre processus de génération personnalisé. Si vous utilisez TFS comme un environnement de construction, vous pouvez suivre les instructions de l’étape suivante pour ajouter la génération de package Azure à votre processus de génération.

## <a name="3-build-a-package-using-tfs-team-build"></a>3 : créer un Package à l’aide de TFS Team Build

Si vous avez défini comme un contrôleur de build et le serveur de génération Team Foundation Server (TFS) configurée en tant qu’un ordinateur de build TFS, puis vous pouvez éventuellement configurer une génération automatisée pour votre package Azure. Pour plus d’informations sur la façon de configurer et utiliser le serveur Team Foundation comme un système de génération, voir [mise à l’échelle votre système de génération][]. En particulier, la procédure suivante suppose que vous avez configuré votre serveur de génération comme décrit dans [déployer et configurer un serveur de génération][], et que vous avez créé un projet d’équipe, créé un projet de service cloud dans le projet d’équipe.

Pour configurer TFS pour créer des packages d’Azure, effectuez les opérations suivantes :

1.  Dans Visual Studio sur votre ordinateur de développement, dans le menu Affichage, choisissez **L’Explorateur d’équipes**ou Ctrl +\\, Ctrl + M. Dans la fenêtre Explorateur d’équipes, développez le nœud **crée** ou cliquez sur la page **crée** et sélectionnez **Nouvelle définition de Build**.

    ![Nouvelle option de définition de Build][0]

2.  Choisissez l’onglet **déclencheur** , puis spécifiez les conditions souhaitées pour lorsque vous souhaitez que le package à générer. Par exemple, spécifiez **L’intégration continue** pour créer le package chaque fois qu’une source contrôle à cocher connexion apparaît.

3.  Sélectionnez l’onglet **Paramètres de la Source** , puis vérifiez que votre dossier de projet est répertorié dans la colonne **Source contrôle dossier** , et le statut est **actif**.

4.  Cliquez sur l’onglet **Créer par défaut** , puis sous contrôleur de Build, vérifiez le nom du serveur de génération.  En outre, choisissez l’option **Copier sortie de build dans le dossier de destination suivant** et spécifiez l’emplacement de déplacement souhaitée.

5.  Sélectionnez l’onglet **processus** . Sous l’onglet processus, sélectionnez le modèle par défaut, sous **créer**, choisissez le projet s’il n’est pas déjà sélectionnée, développez la section **Options avancées** dans la section **Générer** de la grille.

6.  Choisissez **Arguments MSBuild**et définissez les arguments de ligne de commande MSBuild appropriés comme décrit à l’étape 2 ci-dessus. Par exemple, entrez **/t : publier /p:PublishDir =\\\\myserver\\supprime\\ ** pour créer un package et copier les fichiers de package vers l’emplacement \\ \\myserver\\supprime\\:

    ![Arguments MSBuild][2]

    **Remarque :** Copie des fichiers sur un partage public facilite le déploiement manuellement des packages à partir de votre ordinateur de développement.

5.  Tester le succès de votre étape de génération en vérifiant dans une modification de votre projet, ou jusqu'à une nouvelle version file d’attente. Pour en file d’attente d’une nouvelle version, dans l’Explorateur d’équipe, avec le bouton droit de **Toutes les définitions de créer** , puis sélectionnez **File d’attente une nouvelle Build**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4 : publier un Package à l’aide d’un PowerShell Script

Cette section décrit comment créer un script Windows PowerShell de publication la sortie de package d’application Cloud sur Azure à l’aide de paramètres facultatifs. Ce script peut être appelé après la génération étape dans votre automation de génération personnalisée. Il peut également être appelé à partir d’activités de flux de travail de modèle de processus dans Visual Studio TFS Team Build.

1.  Installer les [applets de commande PowerShell Azure][] (v0.6.1 ou une version ultérieure).
    Pendant la phase d’installation applet de commande choisir d’installer un composant logiciel enfichable. Notez que cette version bénéficiant remplace l’ancienne version proposée par le biais CodePlex, bien que les versions précédentes ont été numérotées 2.x.x.

2.  Démarrez PowerShell Azure à l’aide du menu Démarrer ou page. Si vous démarrez de cette façon, les applets de commande PowerShell Azure sera chargé.

3.  À l’invite PowerShell, vérifiez que les applets de commande PowerShell sont chargées en entrant la commande partielle `Get-Azure` puis en appuyant sur la touche Tab pour la saisie semi-automatique.

    Si vous appuyez sur la touche Tab à plusieurs reprises, vous devriez voir les différentes commandes PowerShell Azure.

4.  Vérifiez que vous pouvez vous connecter à votre abonnement Azure en important des informations de votre abonnement à partir du fichier .publishsettings.

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    Puis entrez la commande

    `Get-AzureSubscription`

    Ceci affiche des informations sur votre abonnement. Vérifiez que tout est correct.

4.  Enregistrer le modèle de script fourni à la fin de cet article dans votre dossier des scripts sous la forme c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Passez en revue la section Paramètres du script. Ajouter ou modifier des valeurs par défaut. Ces valeurs peuvent toujours être remplacées en passant des paramètres explicites.

6.  Assurez-vous que les comptes de service et espace de stockage cloud valide créés dans votre abonnement pouvant être exploitées par le script publier. Le compte de stockage (stockage d’objets blob) servira pour télécharger et stocker temporairement le fichier de package et config déploiement lors de la création du déploiement.

    -   Pour créer un nouveau service cloud, vous pouvez appeler ce script ou utiliser le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Le nom du service cloud servira un préfixe dans un nom de domaine complet et par conséquent, elle doit être unique.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Pour créer un nouveau compte de stockage, vous pouvez appeler ce script ou utiliser le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Le nom de compte de stockage servira un préfixe dans un nom de domaine complet et par conséquent, elle doit être unique. Vous pouvez essayer d’utiliser le même nom que le service cloud.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Appeler le script directement à partir d’Azure PowerShell ou associer ce script à votre automatisation hôte de la compilation soit postérieure à la version du package.

    >[AZURE.IMPORTANT] Le script doit toujours supprimer ou remplacer votre déploiements existants par défaut si elles sont détectées. Cela est nécessaire pour activer la remise continue à partir d’automatisation où il est possible d’aucune invite utilisateur.

    **Exemple de scénario 1 :** déploiement continue à l’environnement intermédiaire d’un service :

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Il est généralement suivi tests de vérification et une permutation VIP. La permutation VIP peut être effectuée via le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885) ou à l’aide de l’applet de commande Move-déploiement.

    **Exemple de scénario 2 :** déploiement continue à l’environnement de production d’un service de test dédié

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Bureau à distance :**

    Si le Bureau à distance est activé dans votre projet Azure, que vous devez effectuer des étapes supplémentaires uniques pour vous assurer que le certificat de Service Cloud approprié est téléchargé dans tous les services de cloud ciblées par ce script.

    Recherchez les valeurs empreinte numérique du certificat attendus par vos rôles. Les valeurs de l’empreinte numérique sont affichent dans la section certificats du fichier de configuration de cloud (c'est-à-dire ServiceConfiguration.Cloud.cscfg). Il est également visible dans la boîte de dialogue Configuration de bureau à distance dans Visual Studio lorsque vous affichez les Options et afficher le certificat sélectionné.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Charger des certificats de bureau à distance comme une étape de configuration uniques en utilisant le script applet de commande suivante :

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Par exemple :

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Vous pouvez également exporter le fichier de certificat PFX avec clé privée et télécharger des certificats sur chaque service cloud cible à l’aide du [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 
    Consultez l’article suivant pour en savoir plus : [] [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx].

    **Mise à niveau de déploiement de déploiement par rapport à SUPPR -\> nouveau déploiement**

    Le script effectue par défaut un déploiement de mise à niveau ($enableDeploymentUpgrade = 1) lorsqu’aucun paramètre est passé dans ou la valeur 1 est passée explicitement. Instances unique cela a l’avantage de prendre moins de temps qu’un déploiement complet. Pour les instances qui nécessitent une disponibilité élevée que cela présente également l’avantage de quitter amené en cours d’exécution tandis que d’autres sont mis à niveau (parcourant votre domaine de mise à jour), ainsi que votre VIP n’est pas supprimé.

    Déploiement de mise à niveau peut être désactivée dans le script ($enableDeploymentUpgrade = 0) ou en passant *enableDeploymentUpgrade - 0* en tant que paramètre, ce qui modifie le comportement de script pour tout d’abord supprimer tout déploiement existant, puis créer un nouveau déploiement.

    >[AZURE.IMPORTANT] Le script doit toujours supprimer ou remplacer votre déploiements existants par défaut si elles sont détectées. Cela est nécessaire pour activer la remise continue à partir d’automatisation lorsqu’aucune invite utilisateur/opérateur n’est possible.

## <a name="5-publish-a-package-using-tfs-team-build"></a>5 : publier un Package à l’aide de TFS Team Build

Cette étape facultative connecte TFS Team Build pour le script créé à l’étape 4, qui gère la publication de la version de package pour Azure. Cela implique de modifier le modèle de processus utilisé par votre définition de build afin qu’elle s’exécute une activité de publication à la fin du flux de travail. L’activité publier exécutera votre commande PowerShell passant dans les paramètres de la génération. Résultat de la MSBuild cible et publier le script sera redirigé dans la sortie de génération standard.

1.  Modifier la définition de Build responsable continue déployer.

2.  Sélectionnez l’onglet **processus** .

3.  Suivez [ces instructions](http://msdn.microsoft.com/library/dd647551.aspx) pour ajouter un projet d’activité pour le modèle de processus de génération télécharger le modèle par défaut, ajoutez au projet et archiver. Donnez au modèle de processus de créer un nouveau nom, tel que AzureBuildProcessTemplate.

3.  Revenir à l’onglet **processus** et utilisez **Afficher les détails** pour afficher une liste des modèles de processus de génération disponibles. Cliquez sur le bouton **nouveau...** , puis naviguez jusqu’au projet que vous venez d’ajouter et archivé. Recherchez le modèle que vous venez de créer et cliquez sur **OK**.

4.  Ouvrez le modèle de processus sélectionné pour modification. Vous pouvez ouvrir directement dans le Concepteur de flux de travail ou dans l’éditeur XML pour l’utiliser avec le XAML.

5.  Ajouter la liste suivante d’arguments nouvelles lignes séparées dans l’onglet arguments du Concepteur de flux de travail. Tous les arguments doivent avoir le sens = dans, puis tapez = chaîne. Ces servira à paramètres du flux à partir de la définition de build dans le flux de travail, puis obtenir utilisé pour appeler le script publier.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![Liste d’arguments.][3]

    Le code XAML correspondant ressemble à ceci :

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Ajouter une nouvelle séquence à la fin de s’exécuter sur l’Agent :

    1.  Commencez par ajouter dure une instruction si pour vérifier pour un fichier de script valide. Définir la condition à cette valeur :

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Dans l’ensuite le cas de l’instruction si, ajouter une nouvelle activité de séquence. Définir le nom complet pour « Début publier »

    3.  Avec le début publier séquence toujours sélectionnée, ajouter la liste suivante de nouvelles variables en tant que lignes séparées dans l’onglet variables du Concepteur de flux de travail. Toutes les variables doivent avoir le type de Variable = chaîne et étendue = début publier. Ces servira à paramètres du flux à partir de la définition de build dans le flux de travail, puis obtenir utilisé pour appeler le script publier.

        -   SubscriptionDataFilePath, de type String

        -   PublishScriptFilePath, de type String

            ![Nouvelles variables][4]

    4.  Si vous utilisez TFS 2012 ou une version antérieure, ajoutez une activité ConvertWorkspaceItem au début de la nouvelle séquence. Si vous utilisez TFS 2013 ou une version ultérieure, ajoutez une activité GetLocalPath au début de la nouvelle séquence. Pour un ConvertWorkspaceItem, définissez les propriétés comme suit : Direction = ServerToLocal, DisplayName = « Convertir publier nom du fichier de script », entrée = 'PublishScriptLocation', résultat = 'PublishScriptFilePath', espace de travail = 'Espace de travail'. Pour une activité GetLocalPath, définissez la propriété IncomingPath à « PublishScriptLocation » et le résultat pour « PublishScriptFilePath ». Cette activité convertit le chemin d’accès au script publier à partir d’emplacements TFS server (le cas échéant) à un chemin d’accès de disque local standard.

    5.  Si vous utilisez TFS 2012 ou une version antérieure, ajoutez un autre activité ConvertWorkspaceItem à la fin de la nouvelle séquence. Direction = ServerToLocal, DisplayName = 'T Convert filename abonnement', entrée = 'SubscriptionDataFileLocation', résultat = 'SubscriptionDataFilePath', espace de travail = 'Espace de travail'. Si vous utilisez TFS 2013 ou version ultérieure, ajoutez un autre GetLocalPath. IncomingPath = 'SubscriptionDataFileLocation' et résultat = 'SubscriptionDataFilePath'.

    6.  Ajouter une activité InvokeProcess à la fin de la nouvelle séquence.
        Cette activité appelle PowerShell.exe avec les arguments passés par la définition de Build.

        1.  Arguments = String.Format («-fichier « « {0} » » - serviceName {1} - storageAccountName \{2\ - packageLocation « « {3} » » - cloudConfigLocation « « {4} » » - subscriptionDataFile « « {5} » » - selectedSubscription {6}-environnement « « {7} » » », PublishScriptFilePath, ServiceName%, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, environnement)

        2.  Nom complet = Execute publier script

        3.  Nom de fichier = « PowerShell » (en incluant les guillemets)

        4.  OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  Dans la section **Gérer la sortie Standard** zone de texte de la InvokeProcess, définissez la valeur de la zone de texte à « données ». Il s’agit d’une variable pour stocker les données de sortie standard.

    8.  Ajouter une activité WriteBuildMessage juste en dessous de la section **Gérer la sortie Standard** . Définir l’Importance = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' et le Message = « données ». Cela garantit la sortie standard du script sera écrite à la sortie de génération.

    9.  Dans la section **Gérer sortie d’erreur** zone de texte de la InvokeProcess, définissez la valeur de la zone de texte à « données ». Il s’agit d’une variable pour stocker les données d’erreur.

    10. Ajouter une activité WriteBuildError juste en dessous de la section **Gérer de sortie d’erreur** . Définir le Message = « données ». Ainsi, que les erreurs standard du script sera écrite à la sortie d’erreur de génération.

    11. Corriger les erreurs, indiqués par les points d’exclamation bleus. Pointez sur les points d’exclamation pour obtenir une indication de l’erreur. Enregistrer le flux de travail pour effacer les erreurs.

    Le résultat final des activités de flux de travail publier ressemble à ceci dans le concepteur :

    ![Activités de flux de travail][5]

    Le résultat final des activités de flux de travail publier ressemble à ceci en XAML :

        <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
            <If.Then>
              <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
                <Sequence.Variables>
                  <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                  <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                </Sequence.Variables>
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                  <mtbwa:InvokeProcess.ErrorDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.ErrorDataReceived>
                  <mtbwa:InvokeProcess.OutputDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.OutputDataReceived>
                </mtbwa:InvokeProcess>
              </Sequence>
            </If.Then>
          </If>
        </Sequence>


7.  Enregistrer le flux de travail modèle processus et archiver ce fichier.

8.  Modifier la définition de build (fermez-le si elle est déjà ouverte), puis sélectionnez le bouton **Nouveau** si vous ne voyez pas encore le nouveau modèle dans la liste des modèles de processus.

9.  Définissez le paramètre de propriété valeurs dans la section divers comme suit :

    1.  CloudConfigLocation ='c:\\supprime\\app.publish\\ServiceConfiguration.Cloud.cscfg' *cette valeur est dérivée de : ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = ' c:\\supprime\\app.publish\\ContactManager.Azure.cspkg' *cette valeur est dérivée de : ($PublishDir)($ProjectName) .cspkg*

    3.  PublishScriptLocation = ' c:\\scripts\\WindowsAzure\\PublishCloudService.ps1 »

    4.  ServiceName = 'mycloudservicename' *Utilisez le nom de service cloud approprié ici*

    5.  Environnement = « Intermédiaire »

    6.  StorageAccountName = 'mystorageaccountname' *Utilisez le nom de compte de stockage approprié ici*

    7.  SubscriptionDataFileLocation = ' c:\\scripts\\WindowsAzure\\Subscription.xml »

    8.  SubscriptionName = « par défaut »

    ![Valeurs de propriété de paramètre][6]

10. Enregistrer les modifications dans la définition de Build.

11. File d’attente une Build à exécuter la version du package et publier. Si vous avez un déclencheur définir pour l’intégration continue, vous allez exécuter ce comportement sur chaque archivage.

### <a name="publishcloudserviceps1-script-template"></a>Modèle de script PublishCloudService.ps1

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Étapes suivantes

Pour activer le débogage distant lors de l’utilisation de remise continue, voir [Activer débogage lors de l’utilisation de remise continue à publier sur Azure distant](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

  [Remise continue à Azure à l’aide de Visual Studio Team Services]: cloud-services-continuous-delivery-use-vso.md  
  [Service Team Foundation Build]: https://msdn.microsoft.com/library/ee259687.aspx
  [.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
  [.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
  [.NET framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
  [Évoluer votre système de génération]: https://msdn.microsoft.com/library/dd793166.aspx
  [Déployer et configurer un serveur de génération]: https://msdn.microsoft.com/library/ms181712.aspx
  [Applets de commande PowerShell Azure]: powershell-install-configure.md
  [the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
