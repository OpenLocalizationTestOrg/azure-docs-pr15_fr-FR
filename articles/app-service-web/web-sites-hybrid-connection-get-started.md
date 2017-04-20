<properties 
    pageTitle="Accéder à des ressources locales à l’aide de connexions hybride dans le Service d’application Azure" 
    description="Créer une connexion entre une application web dans le Service d’application Azure et d’une ressource locale qui utilise un port TCP statique" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/03/2016" 
    ms.author="cephalin"/>

#<a name="access-on-premises-resources-using-hybrid-connections-in-azure-app-service"></a>Accéder à des ressources locales à l’aide de connexions hybride dans le Service d’application Azure

Vous pouvez vous connecter à une application de Service d’application Azure à toutes les ressources locales qui utilise un port TCP statique, tels que SQL Server, MySQL, API Web HTTP et la plupart des Services Web personnalisée. Cet article vous explique comment créer une connexion hybride entre application Service et une base de données SQL Server locale.

> [AZURE.NOTE] La partie Web Apps de la fonctionnalité de connexions hybride est disponible uniquement dans le [Portail Azure](https://portal.azure.com). Pour créer une connexion dans les Services BizTalk, voir [Connexions hybride](http://go.microsoft.com/fwlink/p/?LinkID=397274). 
> 
> Ce contenu s’applique également aux applications mobiles dans le Service d’application Azure. 

## <a name="prerequisites"></a>Conditions préalables
- Un abonnement Azure. Pour un abonnement gratuit, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 
 
    Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

- Pour utiliser une base de données SQL Server ou SQL Server Express en local avec une connexion hybride, TCP/IP doit être activé sur un port statique. À l’aide d’une instance par défaut sur SQL Server est recommandée, car elle utilise le port statique 1433. Pour plus d’informations sur l’installation et de configuration SQL Server Express pour une utilisation avec les connexions hybrides, voir [se connecter à un serveur SQL local à partir d’un site web Azure à l’aide de connexions hybride](http://go.microsoft.com/fwlink/?LinkID=397979).

- L’ordinateur sur lequel vous installez l’agent de gestionnaire de connexions hybride local décrite plus loin dans cet article :

    - Doit être en mesure de vous connecter à Azure via le port 5671
    - Doit être en mesure d’atteindre le *nom d’hôte*:*numéro de port* de la ressource locale. 

> [AZURE.NOTE] Les étapes décrites dans cet article part du principe que vous utilisez le navigateur de l’ordinateur qui héberge l’agent de connexion hybride local.


## <a name="create-a-web-app-in-the-azure-portal"></a>Créer une application web dans le portail d’Azure ##

> [AZURE.NOTE] Si vous avez déjà créé un web app ou une application Mobile principal dans le portail Azure que vous souhaitez utiliser pour ce didacticiel, vous pouvez passer directement à [créer une connexion hybride et un BizTalk Service](#CreateHC) et démarrer à partir de cet emplacement.

1. Dans le coin supérieur gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Web + Mobile** > **Web App**.
    
    ![Nouvelle application web][NewWebsite]
    
2. Sur la carte **dans le navigateur** , fournissent une URL et cliquez sur **créer**. 
    
    ![Nom du site Web][WebsiteCreationBlade]
    
3. Après quelques instants, l’application web est créée et sa carte de l’application web s’affiche. La carte est un tableau de bord verticalement défilement qui vous permet de gérer votre site.
    
    ![Site Web en cours d’exécution][WebSiteRunningBlade]
    
4. Pour vérifier que le site est en ligne, vous pouvez cliquer sur l’icône **Parcourir** pour afficher la page par défaut.
    
    ![Cliquez sur Parcourir pour afficher votre application web][Browse]
    
    ![Page de l’application web par défaut][DefaultWebSitePage]
    
Ensuite, vous allez créer une connexion hybride et un service BizTalk pour l’application web.

<a name="CreateHC"></a>
## <a name="create-a-hybrid-connection-and-a-biztalk-service"></a>Créer une connexion hybride et un Service BizTalk ##

1. Dans votre carte de l’application web, cliquez sur **tous les paramètres** > **réseau** > **configurer vos points de terminaison de connexion hybride**.
    
    ![Connexions hybride][CreateHCHCIcon]
    
2. Dans la carte de connexions hybride, cliquez sur **Ajouter**.
    
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
    
3. La carte **d’Ajouter une connexion hybride** s’ouvre.  Dans la mesure où il s’agit de votre première connexion hybride, l’option **nouvelle connexion hybride** est présélectionnée, et la carte de **créer une connexion hybride** s’ouvre pour vous.
    
    ![Créer une connexion hybride][TwinCreateHCBlades]
    
    Sur la **carte de connexion hybride créer**:
    - Pour **nom**, attribuez un nom pour la connexion.
    - Pour **nom d’hôte**, entrez le nom de l’ordinateur local qui héberge la ressource.
    - **Port**, entrez le numéro de port que votre ressource en local utilise (1433 pour une instance SQL Server par défaut).
    - Cliquez sur **argumentaire entreprise parler Service**


4. La carte de **Création de BizTalk Service** s’ouvre. Entrez un nom pour le service BizTalk, puis cliquez sur **OK**.
    
    ![Créer le service BizTalk][CreateHCCreateBTS]
    
    La carte de **Création de BizTalk Service** se ferme et vous revenez à la carte de **créer une connexion hybride** .
    
5. Dans la carte de connexion hybride de créer, cliquez sur **OK**. 
    
    ![Cliquez sur OK][CreateBTScomplete]
    
6. Lorsque le processus se termine, la zone de notification dans le portail vous informe que la connexion a été créée.
    < ! situés à TODO

    Tout échoue lors de cette étape. Je ne parviens pas à créer un service BizTalk dans le portail dogfood. J’ai basculer vers le portail classique (portail complète) et créé le service BizTalk mais il ne fait rien pour vous permettre de lier les - lorsque vous avez terminé l’étape de connexion hybride créer, vous obtenez l’erreur suivante la création de connexion hybride RelecIoudHC a échoué. Le type de ressource est introuvable dans l’espace de noms « Microsoft.BizTaIkServices pour api version 2014-06-01 ».
    
    L’erreur indique qu’il n’a pas pu trouver le type, pas l’instance.
    ![Notification de succès][CreateHCSuccessNotification]
    -->
7. Sur la carte de l’application web, l’icône **connexions hybride** indique maintenant que 1 connexion hybride a été créée.
    
    ![Connexion d’un environnement hybride créée][CreateHCOneConnectionCreated]
    
À ce stade, vous avez terminé une partie importante de l’infrastructure cloud hybride connexion. Ensuite, vous allez créer une pièce locale correspondante.

<a name="InstallHCM"></a>
## <a name="install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>Installer le Gestionnaire de connexions hybride local pour effectuer la connexion ##

1. Sur la carte de l’application web, cliquez sur **tous les paramètres** > **réseau** > **configurer vos points de terminaison de connexion hybride**. 
    
    ![Icône de connexions hybride][HCIcon]
    
2. Sur la carte de **connexions hybride** , la colonne **statut** pour le point de terminaison récemment ajoutée affiche **pas connecté**. Cliquez sur la connexion pour le configurer.
    
    ![Non connecté][NotConnected]
    
    La carte de connexion hybride s’ouvre.
    
    ![NotConnectedBlade][NotConnectedBlade]
    
3. Dans la carte, cliquez sur **Le programme d’installation récepteur**.
    
    ![Cliquez sur le programme d’installation récepteur][ClickListenerSetup]
    
4. La carte de **Propriétés de connexion hybride** s’ouvre. Sous **Gestionnaire de connexions hybrides en local**, sélectionnez **Cliquez ici pour installer**.
    
    ![Cliquez ici pour installer][ClickToInstallHCM]
    
5. Dans l’Application exécutée avertissement boîte de dialogue sécurité, cliquez sur **exécuter** pour continuer.
    
    ![Sélectionnez Exécuter pour continuer][ApplicationRunWarning]
    
6.  Dans la boîte de dialogue **Contrôle de compte d’utilisateur** , cliquez sur **Oui**.
    
    ![Cliquez sur Oui][UAC]
    
7. Le Gestionnaire de connexions hybride est téléchargé et installé pour vous. 
    
    ![L’installation][HCMInstalling]
    
8. Une fois l’installation terminée, cliquez sur **Fermer**.
    
    ![Cliquez sur Fermer][HCMInstallComplete]
    
    Sur la carte de **connexions hybride** , la colonne **statut** indique maintenant **connecté**. 
    
    ![État connecté][HCStatusConnected]

Maintenant que l’infrastructure de connexion hybride est terminée, vous pouvez créer une application hybride qui utilise. 

>[AZURE.NOTE]Les sections suivantes vous montrent comment utiliser une connexion hybride avec un projet principal Mobile applications .NET.

## <a name="configure-the-mobile-app-net-backend-project-to-connect-to-the-sql-server-database"></a>Configurer le projet principal Mobile application .NET pour vous connecter à la base de données SQL Server

Dans le Service d’application, un projet principal Mobile applications .NET est simplement un ASP.NET web app avec un supplémentaires applications Kit de développement Mobile installé et initialisé. Pour utiliser votre application web comme une version de serveur d’applications Mobile, vous devez [télécharger et initialisation le système principal Mobile applications .NET SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk).  

Pour les applications mobiles, vous devez également définir une chaîne de connexion pour la base de données locale ou de modifier le serveur principal pour utiliser cette connexion. 

1. Dans l’Explorateur de solutions dans Visual Studio, ouvrez le fichier Web.config pour votre serveur principal .NET de l’application Mobile, recherchez la section **connectionStrings** , ajouter une nouvelle entrée SqlClient l’exemple suivant, qui pointe vers la base de données SQL Server en local :

        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

    N’oubliez pas de remplacer `<**secure_password**>` dans cette chaîne avec le mot de passe que vous avez créé pour *HybridConnectionLogin*.

3. Cliquez sur **Enregistrer** dans Visual Studio pour enregistrer le fichier Web.config.

    > [AZURE.NOTE]Ce paramètre de connexion est utilisé lors de l’exécution sur l’ordinateur local. Lorsque vous exécutez dans Azure, ce paramètre est remplacé par le paramètre de connexion défini dans le portail.

4. Développez le dossier **modèles** et ouvrez le fichier de modèle de données, qui se termine par *Context.cs*.

6. Modifiez le constructeur instance **DbContext** pour passer la valeur `OnPremisesDBConnection` au constructeur **DbContext** base, similaire à l’extrait de code suivante :

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

    Le service utilisera maintenant la nouvelle connexion à la base de données SQL Server.

## <a name="update-the-mobile-app-backend-to-use-the-on-premises-connection-string"></a>Mettre à jour le serveur principal de l’application Mobile pour utiliser la chaîne de connexion en local

Ensuite, vous devez ajouter un paramètre d’application pour cette nouvelle chaîne de connexion afin qu’il puisse être utilisé à partir d’Azure.  

1. Dans le [portail Azure](https://portal.azure.com) dans le code de serveur principal d’application web pour votre application Mobile, cliquez sur **tous les paramètres**, puis sur **paramètres de l’Application**.

3. Dans la carte de **paramètres de l’application Web** , faites défiler jusqu'à **chaînes de connexion** et ajouter une nouvelle chaîne de connexion **SQL Server** nommée `OnPremisesDBConnection` avec une valeur telle que `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.

    Remplacer `<**secure_password**>` avec le mot de passe sécurisé pour votre base de données locale.

    ![Chaîne de connexion de base de données locale](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)

2. Appuyez sur **Enregistrer** pour enregistrer la connexion hybride et la chaîne de connexion que vous venez de créer.

À ce stade, vous pouvez publier à nouveau le projet serveur et tester la nouvelle connexion avec vos clients applications Mobile actuels. Données sont lues et écrites dans la base de données locale à l’aide de la connexion hybride.

<a name="NextSteps"></a>
## <a name="next-steps"></a>Étapes suivantes ##

- Pour plus d’informations sur la création d’une application web ASP.NET qui utilise une connexion hybrides, voir [se connecter à un serveur SQL local à partir d’un site web Azure à l’aide de connexions hybride](http://go.microsoft.com/fwlink/?LinkID=397979). 

### <a name="additional-resources"></a>Ressources supplémentaires

[Vue d’ensemble des connexions hybride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist présente les connexions hybride (9 de canal vidéo)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site web de connexions hybride](https://azure.microsoft.com/services/biztalk-services/)

[BizTalk Services : Onglets de tableau de bord, moniteur, échelle, configurer et connexion hybride](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Création d’un nuage hybride réelles avec la portabilité des applications transparente (9 de canal vidéo)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Se connecter à un serveur SQL local à partir d’Azure Mobile Services à l’aide de connexions hybride (9 de canal vidéo)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 