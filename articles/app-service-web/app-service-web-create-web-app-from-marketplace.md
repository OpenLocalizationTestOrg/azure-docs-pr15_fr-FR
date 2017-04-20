<properties
    pageTitle="Créer une application web à partir de la Azure Marketplace | Microsoft Azure"
    description="Apprenez à créer une nouvelle application web WordPress depuis la Azure Marketplace à l’aide du portail Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>Créer une application web à partir de la Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

La Azure Marketplace met à disposition un large éventail d’applications web les plus courants sont développés par Microsoft, des entreprises tierces et initiatives logiciel open source. Par exemple, WordPress, Umbraco CMS, Drupal, etc. Ces applications web sont appuient sur un large éventail de structures populaires, telles que [PHP] cette WordPress exemple [.NET], [Node.js], [Java]et [Python], pour ne citer. Pour créer une application web à partir de la Azure Marketplace le logiciel seulement que vous avez besoin est le navigateur que vous utilisez pour le [Portail Azure].

Dans ce didacticiel, vous allez découvrir comment :

* Trouver et à créer l’application web dans le Service d’application Azure qui est basé sur un modèle Azure Marketplace.
* Configurer les paramètres de Service d’application Azure pour la nouvelle application web.
* Lancer et gérer votre application web.

Aux fins de ce didacticiel, vous allez déployer un site de blog WordPress depuis la Azure Marketplace. Quand vous avez terminé les étapes décrites dans ce didacticiel, vous disposez de votre propre site WordPress vers le haut et en cours d’exécution dans le cloud.

![Tableau de bord application exemple WordPress wep][WordPressDashboard1]

Le site WordPress que vous allez déployer dans ce didacticiel utilise MySQL pour la base de données. Si vous souhaitez utiliser à la place de la base de données SQL pour la base de données, voir [Nami de projet], qui est également disponible via le Azure Marketplace.

> [AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas un compte, vous pouvez [activer vos avantages d’abonné Visual Studio] [ activate] ou [s’inscrire à une version d’évaluation gratuite][free trial].
>
> Si vous voulez commencer avec le Service d’application Azure avant de vous inscrivez à un compte Azure, accédez à [Essayer le Service application]. À partir de là, vous pouvez immédiatement créer une application web starter courte dans le Service d’application : aucune carte de crédit n’est nécessaire, et il n’existe aucun engagements.

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Trouver et à créer une application Web dans le Service d’application Azure

1. Connectez-vous au [portail Azure].

1. Cliquez sur **Nouveau**.
    
    ![Créer une nouvelle ressource Azure][MarketplaceStart]
    
1. Recherchez **WordPress**, puis cliquez sur **WordPress**. (Si vous souhaitez utiliser la base de données SQL au lieu de MySQL, recherchez **Project Nami**.)

    ![Recherchez WordPress sur le marché][MarketplaceSearch]
    
1. Après la lecture de la description de l’application WordPress, cliquez sur **créer**.

    ![Créer WordPress dans le navigateur][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configurer les paramètres de Service application Azure pour votre nouvelle application Web

1. Après avoir créé une nouvelle application web, la carte de paramètres WordPress s’affiche, que vous utiliserez pour effectuer les opérations suivantes :

    ![Configurer les paramètres de l’application web WordPress][ConfigStart]

1. Entrez un nom pour l’application web dans la zone **dans le navigateur** .

    Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera *{nom}*. azurewebsites.net. Si le nom que vous entrez n’est pas unique, un point d’exclamation rouge apparaît dans la zone de texte.

    ![Configurer le nom de l’application web WordPress][ConfigAppName]

1. Si vous avez plusieurs abonnements, choisissez celui que vous voulez utiliser. 

    ![Configurer l’abonnement pour l’application web][ConfigSubscription]

1. Sélectionnez un **Groupe de ressources** ou créez-en une.

    Pour plus d’informations sur les groupes de ressources, voir [vue d’ensemble du Gestionnaire de ressources Azure][ResourceGroups].

    ![Configurer le groupe de ressources pour l’application web][ConfigResourceGroup]

1. Sélectionnez un **Emplacement de plan de Service** d’application/ou créez-en une.

    Pour plus d’informations sur les offres de Service d’application, voir [vue d’ensemble des plans Azure Application Service][AzureAppServicePlans]. 

    ![Configurer le plan de service pour l’application web][ConfigServicePlan]

1. Cliquez sur **base de données**et dans la carte de **Nouvelle base de données MySQL** fournir les valeurs requises pour la configuration de votre base de données MySQL.

    un. Entrez un nouveau nom ou laissez le nom par défaut.

    b. Conserver le **Type de base de données** définie sur **partagé**.

    c. Cliquez sur le même emplacement que celui que vous avez choisi pour l’application web.

    d. Choisissez un niveau de tarification. **Mercure** , qui est disponible avec les connexions minimales et espace disque - est prêt pour ce didacticiel.

    e. Dans la carte de la **Nouvelle base de données MySQL** , acceptez les conditions juridiques, puis cliquez sur **OK**. 

    ![Configurer les paramètres de base de données pour l’application web][ConfigDatabase]

1. Dans la carte **WordPress** , acceptez les conditions juridiques et puis cliquez sur **créer**. 

    ![Terminer les paramètres de l’application web et cliquez sur OK][ConfigFinished]

    Le Service application Azure crée l’application web, généralement en moins d’une minute. Vous pouvez surveiller la progression en cliquant sur l’icône bell en haut de la page du portail.

    ![Indicateur de progression][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Lancer et gérer votre application web WordPress
    
1. Lors de la création d’une application web est terminée, naviguez dans le portail Azure au groupe de ressources dans lequel vous avez créé l’application, et vous pouvez voir l’application web et la base de données.

    La ressource supplémentaire avec l’icône ampoule est [Application Insights][ApplicationInsights], qui fournit des services d’analyse pour votre application web.

1. Dans la carte du **groupe de ressources** , cliquez sur la ligne de l’application web.

    ![Sélectionnez votre application web WordPress][WordPressSelect]

1. Dans la carte de l’application Web, cliquez sur **Parcourir**.

    ![Accédez à votre application web WordPress][WordPressBrowse]

1. Si vous êtes invité à sélectionner la langue pour votre blog WordPress, sélectionnez la langue souhaitée, puis sur **Continuer**.

    ![Configurer la langue pour votre application web WordPress][WordPressLanguage]

1. Dans la page **Bienvenue dans** WordPress, entrez les informations de configuration requises par WordPress, puis cliquez sur **Installer WordPress**.

    ![Configurer les paramètres de votre application web WordPress][WordPressConfigure]

1. Connectez-vous en utilisant les informations d’identification que vous avez créé dans la page **Bienvenue** .  

1. Page de tableau de bord de votre site s’ouvre et affiche les informations que vous avez fourni.    

    ![Afficher votre tableau de bord WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à créer et déployer une application web exemple sur la Azure Marketplace.

Pour plus d’informations sur l’utilisation de l’application de Service Web Apps, consultez les liens sur le côté gauche de la page (pour les fenêtres du navigateur large) ou en haut de la page (pour les fenêtres du navigateur étroite).

Pour plus d’informations sur le développement WordPress web apps sur Azure, voir [WordPress développement de service d’application Azure][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Essayez de Service d’application]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portail Azure]: https://portal.azure.com/
[Nami de projet]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
