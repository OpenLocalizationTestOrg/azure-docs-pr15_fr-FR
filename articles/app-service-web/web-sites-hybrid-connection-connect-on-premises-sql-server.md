<properties
    pageTitle="Connectez-vous à locale SQL Server à partir d’une application web dans le Service d’application Azure à l’aide de connexions hybride"
    description="Créer une application web sur Microsoft Azure et connectez-la à une base de données SQL Server en local"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="cephalin"/>

# <a name="connect-to-on-premises-sql-server-from-a-web-app-in-azure-app-service-using-hybrid-connections"></a>Connectez-vous à locale SQL Server à partir d’une application web dans le Service d’application Azure à l’aide de connexions hybride

Connexions hybride peuvent se connecter [Azure Application Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps vers des ressources locales qui utilisent un port TCP statique. Ressources pris en charge comprennent Microsoft SQL Server, MySQL, API Web HTTP, le Service de l’application et la plupart des Services Web personnalisée.

Dans ce didacticiel, vous allez apprendre à créer une application web application Service dans le [Portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), se connecter l’application web à votre base de données SQL Server locale en local à l’aide de la nouvelle fonctionnalité de connexion hybride, créer une application ASP.NET simple qui utilisent la connexion hybride et déploiement de l’application à l’application Service web app. L’application web terminée sur Azure stocke les informations d’identification utilisateur dans une base de données d’appartenance en local. Ce didacticiel, aucune expérience préalable à l’aide de Azure ou ASP.NET.

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.
>
>La partie Web Apps de la fonctionnalité de connexions hybride est disponible uniquement dans le [Portail Azure](https://portal.azure.com). Pour créer une connexion dans les Services BizTalk, voir [Connexions hybride](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## <a name="prerequisites"></a>Conditions préalables ##

Pour effectuer ce didacticiel, vous devez les produits suivants. Toutes les sont disponibles dans les versions gratuites, afin que vous pouvez commencer à développer pour Azure entièrement pour libre.

- **Abonnement azure** - pour un abonnement gratuit, voir [Azure la version d’évaluation gratuite](/pricing/free-trial/).

- **Visual Studio 2013** - télécharger une version d’évaluation gratuite de Visual Studio 2013, voir [Téléchargements de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installer avant de poursuivre.

- **Microsoft .NET Framework 3.5 Service Pack 1** - si votre système d’exploitation Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 ou Windows Server 2008 R2, vous pouvez activer cette option dans le panneau de configuration > Programmes et fonctionnalités > activer des fonctionnalités Windows ou désactiver. Dans le cas contraire, vous pouvez le télécharger à partir du [Centre de téléchargement Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **2014 SQL Server Express avec des outils** - télécharger Microsoft SQL Server Express gratuitement à la [page de base de données plate-forme Microsoft Web](http://www.microsoft.com/web/platform/database.aspx). Choisir la version **Express** (pas LocalDB). La version **Express avec des outils** inclut SQL Server Management Studio, que vous utiliserez dans ce didacticiel.

- **SQL Server Management Studio Express** - ce dernier est fourni avec SQL Server 2014 Express avec téléchargement des outils mentionné ci-dessus, mais si vous avez besoin installer séparément, vous pouvez télécharger et installer à partir de la [page de téléchargement de SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

Le didacticiel suppose que vous avez un abonnement Azure, que vous avez installé Visual Studio 2013 et que vous avez installé ou activé .NET Framework 3.5. Le didacticiel vous montre comment installer SQL Server 2014 Express dans une configuration qui fonctionne correctement avec la fonctionnalité de connexions de hybride Azure (une instance par défaut avec un port TCP statique). Avant de démarrer le didacticiel, téléchargez 2014 SQL Server Express avec des outils à partir de l’emplacement présenté ci-dessus si vous n’avez pas installé SQL Server.

### <a name="notes"></a>Notes ###
Pour utiliser une base de données SQL Server ou SQL Server Express en local avec une connexion hybride, TCP/IP doit être activé sur un port statique. Instances par défaut sur SQL Server utilisent le port statique 1433, tandis que n’est pas le cas des instances nommées.

L’ordinateur sur lequel vous installez l’agent de gestionnaire de connexions hybride local :

- Connectivité sortante vers Azure peut comporter sur :

Port|Pourquoi
---|---
80|**Requis** pour le port HTTP de validation de certificat et éventuellement pour la connectivité de données.
443|**Facultatif** pour la connexion de données. Si la connectivité sortante 443 n’est pas disponible, le port TCP 80 est utilisé.
5671 et 9352|**Recommandé** mais facultatif pour la connexion de données. Remarque : ce mode donne généralement de débit. Si la connectivité sortante à ces ports n’est pas disponible, le port TCP 443 est utilisé.
- Doit être en mesure d’atteindre le *nom d’hôte*:*numéro de port* de la ressource locale.

Les étapes décrites dans cet article part du principe que vous utilisez le navigateur de l’ordinateur qui héberge l’agent de connexion hybride local.

Si vous avez déjà installé dans une configuration et dans un environnement qui remplit les conditions ci-dessus SQL Server, vous pouvez passer directement et commencer par [créer une base de données SQL Server locale](#CreateSQLDB).

<a name="InstallSQL"></a>
## <a name="a-install-sql-server-express-enable-tcpip-and-create-a-sql-server-database-on-premises"></a>A. Installer SQL Server Express, activer le protocole TCP/IP et créer une base de données SQL Server en local ##

Cette section vous montre comment installer SQL Server Express, activer le protocole TCP/IP et créer une base de données pour permettre à votre application web fonctionnera avec le portail Azure.

### <a name="install-sql-server-express"></a>Installer SQL Server Express ###

1. Pour installer SQL Server Express, exécutez le fichier **SQLEXPRWT_x64_ENU.exe** ou **SQLEXPR_x86_ENU.exe** que vous avez téléchargé. L’Assistant centre d’Installation de SQL Server s’affiche.

    ![Installation du serveur SQL][SQLServerInstall]

2. Choisissez **nouvelle installation SQL Server autonome ou ajouter des fonctionnalités à une installation existante**. Suivez les instructions, acceptez les choix par défaut et les paramètres, jusqu'à atteindre la page **Configuration de l’Instance** .

3. Dans la page **Configuration de l’Instance** , sélectionnez **l’instance par défaut**.

    ![Sélectionnez une Instance par défaut][ChooseDefaultInstance]

    Par défaut, l’instance par défaut de SQL Server écoute les demandes des clients de SQL Server sur le port statique 1433, qui est ce que la fonctionnalité de connexions hybride nécessite. Instances nommées utilisent les ports dynamiques et UDP, qui ne sont pas prises en charge par les connexions hybride.

4. Acceptez les paramètres par défaut dans la page de **Configuration du serveur** .

5. Dans la page de **Configuration du moteur de base de données** , sous **Mode d’authentification**, sélectionnez le **Mode mixte (SQL Server et l’authentification Windows)**et fournissez un mot de passe.

    ![Sélectionnez le Mode mixte][ChooseMixedMode]

    Dans ce didacticiel, vous utiliserez l’authentification SQL Server. Veillez à mémoriser le mot de passe que vous fournissez, car vous en aurez besoin ultérieurement.

6. Passez en revue le reste de l’Assistant pour terminer l’installation.

### <a name="enable-tcpip"></a>Activer le protocole TCP/IP ###
Pour activer TCP/IP, vous allez utiliser le Gestionnaire de Configuration SQL Server, qui a été installé lors de l’installation de SQL Server Express. Suivez les étapes décrites dans [Activer le protocole réseau TCP/IP pour SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) avant de poursuivre.

<a name="CreateSQLDB"></a>
### <a name="create-a-sql-server-database-on-premises"></a>Créer une base de données SQL Server en local ###

Votre application web Visual Studio nécessite une base de données d’appartenance sont accessibles par Azure. Cela nécessite une base de données SQL Server ou SQL Server Express (pas la base LocalDB le modèle MVC utilise par défaut), afin que vous allez ensuite créer la base de données d’appartenance.

1. Dans SQL Server Management Studio, connectez-vous à SQL Server que vous venez d’installer. (Si la **connexion au serveur** de boîte de dialogue ne s’affiche pas automatiquement, naviguez jusqu'à **l’Explorateur d’objets** dans le volet gauche, cliquez sur **se connecter**, puis cliquez sur **Le moteur de base de données**.)  ![Se connecter au serveur][SSMSConnectToServer]

    Pour **type de serveur**, choisissez le **Moteur de base de données**. **Nom du serveur**, vous pouvez utiliser **hôte local** ou le nom de l’ordinateur que vous utilisez. Choisissez **l’authentification SQL Server**, puis puis se connecter avec le nom d’utilisateur et le mot de passe que vous avez créé précédemment.

2. Pour créer une nouvelle base de données à l’aide de SQL Server Management Studio, avec le bouton droit de **bases de données** dans l’Explorateur d’objets, puis cliquez sur **Nouvelle base de données**.

    ![Créer la nouvelle base de données][SSMScreateNewDB]

3. Dans la boîte de dialogue **Nouvelle base de données** , entrez MembershipDB pour le nom de la base de données, puis cliquez sur **OK**.

    ![Fournir le nom de la base de données][SSMSprovideDBname]

    Notez que ne pas émettre des modifications à la base de données à ce stade. Les informations d’appartenance sont ajoutées automatiquement ultérieurement par votre application web lors de son exécution.

4. Dans l’Explorateur d’objets, si vous développez des **bases de données**, vous verrez que la base de données d’appartenance a été créé.

    ![MembershipDB créé][SSMSMembershipDBCreated]

<a name="CreateSite"></a>
## <a name="b-create-a-web-app-in-the-azure-portal"></a>B. Créer une application web dans le portail d’Azure ##

> [AZURE.NOTE] Si vous avez déjà créé une application web dans le portail Azure que vous souhaitez utiliser pour ce didacticiel, vous pouvez passer directement à [créer une connexion hybride et un BizTalk Service](#CreateHC) et continuer à partir de là.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Web + Mobile** > **dans le navigateur**.

    ![Bouton Nouveau][New]

2. Configurer votre application web, puis cliquez sur **créer**.

    ![Nom du site Web][WebsiteCreationBlade]

3. Après quelques instants, l’application web est créée et sa carte de l’application web s’affiche. La carte est un tableau de bord verticalement défilement qui vous permet de gérer votre application web.

    ![Site Web en cours d’exécution][WebSiteRunningBlade]

    Pour vérifier que l’application web est en ligne, vous pouvez cliquer sur l’icône **Parcourir** pour afficher la page par défaut.

Ensuite, vous allez créer une connexion hybride et un service BizTalk pour l’application web.

<a name="CreateHC"></a>
## <a name="c-create-a-hybrid-connection-and-a-biztalk-service"></a>C. Créer une connexion hybride et un Service BizTalk ##

1. Revenez dans le portail, accédez à paramètres, puis cliquez sur **mise en réseau** > **configurer vos points de terminaison de connexion hybride**.

    ![Connexions hybride][CreateHCHCIcon]

2. Dans la carte de connexions hybride, cliquez sur **Ajouter** > **nouvelle connexion hybride**.

3. Sur la carte de **créer une connexion hybride** :
    - Pour **nom**, attribuez un nom pour la connexion.
    - Pour **nom d’hôte**, entrez le nom de votre ordinateur hôte SQL Server.
    - **Port**, entrez 1433 (le port par défaut pour SQL Server).
    - Cliquez sur **Le BizTalk Service** > **Nouveau BizTalk Service** et entrez un nom pour le service BizTalk.

    ![Créer une connexion hybride][TwinCreateHCBlades]

5. Cliquez sur **OK** à deux reprises.

    Lorsque le processus se termine, l’est zone **Notifications** flash verte **SUCCESS** et le va carte **connexion hybride** afficher la nouvelle connexion hybride avec l’état comme **non connecté**.

    ![Connexion d’un environnement hybride créée][CreateHCOneConnectionCreated]

À ce stade, vous avez terminé une partie importante de l’infrastructure cloud hybride connexion. Ensuite, vous allez créer une pièce locale correspondante.

<a name="InstallHCM"></a>
## <a name="d-install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>D. Installer le Gestionnaire de connexions hybride local pour effectuer la connexion ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

À présent que l’infrastructure de connexion hybride est terminée, vous allez créer une application web qui l’utilise.

<a name="CreateASPNET"></a>
## <a name="e-create-a-basic-aspnet-web-project-edit-the-database-connection-string-and-run-the-project-locally"></a>E. Créer un projet web ASP.NET simple, modifiez la chaîne de connexion de base de données et exécuter le projet localement ##

### <a name="create-a-basic-aspnet-project"></a>Créer un projet ASP.NET simple ###
1. Dans Visual Studio, dans le menu **fichier** , créez un nouveau projet :

    ![Nouveau projet Visual Studio][HCVSNewProject]

2. Dans la section **modèles** de la boîte de dialogue **Nouveau projet** , sélectionnez **site Web** et choisissez **Application Web ASP.NET**, puis cliquez sur **OK**.

    ![Cliquez sur Application Web ASP.NET][HCVSChooseASPNET]

3. Dans la boîte de dialogue **Nouveau projet ASP.NET** , choisissez **MVC**, puis cliquez sur **OK**.

    ![Choisissez MVC][HCVSChooseMVC]

4. Lorsque le projet a été créé, la page Lisez-moi application s’affiche. Ne fonctionnent pas encore le projet web.

    ![Page Lisez-moi][HCVSReadmePage]

### <a name="edit-the-database-connection-string-for-the-application"></a>Modifier la chaîne de connexion de base de données pour l’application ###

Dans cette étape, vous modifiez la chaîne de connexion qui indique où trouver votre base de données SQL Server Express locale à votre application. La chaîne de connexion est dans un fichier Web.config de l’application, qui contient des informations de configuration pour l’application.

> [AZURE.NOTE] Pour vous assurer que votre application utilise la base de données que vous avez créé dans SQL Server Express et non à celui par défaut de Visual Studio LocalDB, il est important d’effectuer cette étape avant l’exécution de votre projet.

1. Dans l’Explorateur de solutions, double-cliquez sur le fichier Web.config.

    ![Web.config][HCVSChooseWebConfig]

2. Modifiez la section **connectionStrings** pour pointer vers la base de données SQL Server sur votre ordinateur local, en respectant la syntaxe dans l’exemple suivant :

    ![Chaîne de connexion][HCVSConnectionString]

    Lorsque vous composez la chaîne de connexion, gardez à l’esprit les points suivants :

    - Si vous vous connectez à une instance nommée au lieu d’une instance par défaut (par exemple, YourServer\SQLEXPRESS), vous devez configurer votre SQL Server pour utiliser les ports statiques. Pour plus d’informations sur la configuration des ports statiques, Découvrez [comment configurer SQL Server pour écouter un port spécifique](http://support.microsoft.com/kb/823938). Par défaut, les instances nommées utilisent UDP et ports dynamiques, qui ne sont pas prises en charge par les connexions hybride.

    - Il est recommandé de spécifier le port (1433 par défaut, comme illustré dans l’exemple) dans la chaîne de connexion afin que vous pouvez Assurez-vous que votre serveur SQL Server local a TCP activé et qui utilise le port correct.

    - N’oubliez pas d’utiliser l’authentification SQL Server pour vous connecter, spécifiant l’ID d’utilisateur et mot de passe dans la chaîne de connexion.

3. Cliquez sur **Enregistrer** dans Visual Studio pour enregistrer le fichier Web.config.

### <a name="run-the-project-locally-and-register-a-new-user"></a>Exécuter le projet localement et enregistrer un nouvel utilisateur ###

1. À présent, exécutez votre nouveau projet web localement en cliquant sur le bouton Parcourir sous débogage. Cet exemple utilise Internet Explorer.

    ![Exécuter le projet][HCVSRunProject]

2. Dans le coin supérieur droit de la page web par défaut, sélectionnez **Enregistrer** pour enregistrer un nouveau compte :

    ![Enregistrer un nouveau compte][HCVSRegisterLocally]

3. Entrez un nom d’utilisateur et mot de passe :

    ![Entrez le nom d’utilisateur et mot de passe][HCVSCreateNewAccount]

    Cela crée automatiquement une base de données sur votre serveur SQL Server local qui contient les informations d’adhésion pour votre application. Une des tables (**dbo. AspNetUsers**) suspensions web informations d’identification utilisateur de l’application tels que ceux que vous venez d’entrer. Vous verrez ce tableau plus loin dans le didacticiel.

4. Fermez la fenêtre du navigateur de la page web par défaut. L’application dans Visual Studio s’arrête.

Vous êtes maintenant prêt à l’étape suivante, qui consiste à publier l’application sur Azure et tester.

<a name="PubNTest"></a>
## <a name="f-publish-the-web-application-to-azure-and-test-it"></a>F. Publier l’application web vers Azure et tester ##

À présent, vous publiez votre application dans votre application web application Service et tester puis pour voir comment la connexion hybride que vous avez configuré précédemment est utilisée pour connecter votre application web à la base de données sur votre ordinateur local.

### <a name="publish-the-web-application"></a>Publier l’application web ###

1. Vous pouvez télécharger votre profil de publication de l’application web application Service dans le portail Azure. Dans la carte pour votre application web, cliquez sur **obtenir publier le profil**et puis enregistrez le fichier sur votre ordinateur.

    ![Téléchargement publier le profil][PortalDownloadPublishProfile]

    Ensuite, vous allez importer ce fichier dans votre application web Visual Studio.

2. Dans Visual Studio, le nom du projet dans l’Explorateur d’avec le bouton droit et sélectionnez **Publier**.

    ![Sélectionnez Publier][HCVSRightClickProjectSelectPublish]

3. Dans la boîte de dialogue **Publier le site Web** , sous l’onglet **profil** , sélectionnez **Importer**.

    ![Importation][HCVSPublishWebDialogImport]

4. Accédez à votre profil de publication téléchargé, sélectionner, puis cliquez sur **OK**.

    ![Naviguez jusqu’au profil][HCVSBrowseToImportPubProfile]

5. Vos informations de publication sont importées et s’affiche sous l’onglet **connexion** de la boîte de dialogue.

    ![Cliquez sur Publier][HCVSClickPublish]

    Cliquez sur **Publier**.

    Une fois la publication terminée, votre navigateur lancera et afficher votre application ASP.NET maintenant familière--sauf que c’est actif dans le cloud Azure !

Ensuite, vous utiliserez votre application web en direct pour afficher sa connexion hybride en action.

### <a name="test-the-completed-web-application-on-azure"></a>Tester l’application web terminée sur Azure ###

1. Dans la partie supérieure droite de votre page web sur Azure, cliquez sur **se connecter**.

    ![Journal des tests dans][HCTestLogIn]

2. Votre application web application Service est maintenant connectée à la base de données de l’appartenance de votre application web sur votre ordinateur local. Pour vérifier cela, connectez-vous avec les mêmes informations d’identification que vous avez entré dans la base de données locale précédemment.

    ![Carte de vœux Hello][HCTestHelloContoso]

3. Pour tester davantage votre nouvelle connexion hybride, déconnectez-vous de votre application web Azure et enregistrer en tant qu’un autre utilisateur. Fournir un nouveau nom d’utilisateur et mot de passe, puis cliquez sur **Enregistrer**.

    ![Test inscrire un autre utilisateur][HCTestRegisterRelecloud]

4. Pour vérifier que les informations d’identification du nouvel utilisateur ont été stockées dans votre base de données locale via votre connexion hybride, ouvrez SQL Management Studio sur votre ordinateur local. Dans l’Explorateur d’objets, développez la base de données **MembershipDB** , puis puis **Tables**. Menu contextuel le propriétaire **. AspNetUsers** appartenance tableau et choisissez **Sélectionner Top 1000 lignes** pour afficher les résultats.

    ![Afficher les résultats][HCTestSSMSTree]

5. Votre table appartenance au local affiche désormais les deux comptes - celui que vous avez créé localement et celui que vous avez créé dans le cloud Azure. Celui que vous avez créé dans le cloud a été enregistré dans votre base de données locale via la fonctionnalité de connexion hybride d’Azure.

    ![Utilisateurs enregistrés dans la base de données locale][HCTestShowMemberDb]

Vous avez maintenant créé et déployer une application web ASP.NET qui utilise une connexion hybride entre une application web dans le cloud Azure et une base de données SQL Server locale. Félicitations !

## <a name="see-also"></a>Voir aussi ##
[Vue d’ensemble des connexions hybride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist présente les connexions hybride (9 de canal vidéo)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Vue d’ensemble des connexions hybride](/services/biztalk-services/)

[BizTalk Services : Onglets de tableau de bord, moniteur, échelle, configurer et connexion hybride](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Création d’un nuage hybride réelles avec la portabilité des applications transparente (9 de canal vidéo)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Accéder à des ressources locales à l’aide de connexions hybride dans le Service d’application Azure](../app-service-web/web-sites-hybrid-connection-get-started.md)

[Vue d’ensemble de l’identité ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
