<properties 
    pageTitle="Créer une application Web dans le Service d’application Azure à l’aide du Kit de développement Azure pour Java" 
    description="Apprenez à créer une application Web service d’application Azure par programme à l’aide du Kit de développement Azure pour Java." 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Créer une application Web dans le Service d’application Azure à l’aide du Kit de développement Azure pour Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Vue d’ensemble

Cette procédure pas à pas vous montre comment créer un SDK Azure pour application Java qui crée une application Web dans le [Service d’application Azure][], puis déployer une application à celui-ci. Il se compose de deux parties :

- Partie 1 montre comment créer une application Java qui crée une application web.
- Partie 2 montre comment créer un JSP simple « Hello World » application client, puis utiliser FTP pour déployer le code de Service d’application.


## <a name="prerequisites"></a>Conditions préalables

### <a name="software-installations"></a>Installer un logiciel

Le code de l’application AzureWebDemo dans cet article a été rédigé avec Azure Java SDK 0.7.0, que vous pouvez installer à l’aide du [Programme d’installation de la plateforme Web][] (WebPI). En outre, vérifiez que vous utilisez la dernière version du [Kit de ressources Azure pour Eclipse][]. Après avoir installé le Kit de développement, mettre à jour les dépendances dans votre projet Eclipse en exécutant **Mettre à jour un Index** dans les **Référentiels Maven**, puis rajouter la dernière version de chaque module dans la fenêtre des **dépendances** . Vous pouvez vérifier la version de votre logiciel installé dans Eclipse en cliquant sur **Aide > Détails de l’Installation**; Vous devez disposer au moins les versions suivantes :

- Package Microsoft Azure des bibliothèques pour Java 0.7.0.20150309
- Eclipse IDE pour les développeurs EE Java 4.4.2.20150219


### <a name="create-and-configure-cloud-resources-in-azure"></a>Créer et configurer des ressources du Cloud dans Azure

Avant de commencer cette procédure, vous devez posséder un abonnement Azure actif et configurer un Active Directory (AD) sur Azure par défaut.


### <a name="create-an-active-directory-ad-in-azure"></a>Créer un Active Directory (AD) dans Azure

Si vous n’avez pas déjà un Active Directory (AD) sur votre abonnement Azure, connectez-vous au [portail classique Azure][] avec votre compte Microsoft. Si vous avez plusieurs abonnements, cliquez sur **abonnements** , sélectionnez le répertoire par défaut pour l’abonnement que vous souhaitez utiliser pour ce projet. Puis cliquez sur **Appliquer** pour passer à cet affichage de l’abonnement.

1. Sélectionnez **Active Directory** dans le menu situé à gauche. **Cliquez sur Nouveau > répertoire > créer personnalisé**.

2. **Ajout d’un répertoire**, sélectionnez **Créer un répertoire**.

3. Dans la zone **nom**, entrez un nom de répertoire.

4. Dans le **domaine**, entrez un nom de domaine. Il s’agit d’un nom de domaine simple qui est inclus par défaut dans votre annuaire ; Il comporte le formulaire `<domain_name>.onmicrosoft.com`. Vous pouvez nommer selon le nom du répertoire ou un autre nom de domaine dont vous êtes propriétaire. Plus tard, vous pouvez ajouter un autre nom de domaine votre organisation utilise déjà.

5. Dans un **pays ou région**, sélectionnez vos paramètres régionaux.

Pour plus d’informations sur AD, voir [qu’est un répertoire Azure AD][]?


### <a name="create-a-management-certificate-for-azure"></a>Créer un certificat de gestion pour Azure

Azure SDK pour Java utilise gestion des certificats pour vous authentifier avec abonnements Azure. Il s’agit des certificats X.509 v3 que vous permet de s’authentifier une application cliente qui utilise l’API de gestion de Service pour agir au nom du propriétaire de l’abonnement pour gérer les ressources de l’abonnement.

Le code de cette procédure utilise un certificat auto-signé pour vous authentifier avec Azure. Pour cette procédure, vous devez créer un certificat, puis téléchargez-le sur le [portail classique Azure][] au préalable. Cela implique les étapes suivantes :

- Générer un fichier PFX représentant votre certificat client et enregistrez-le localement.
- Générer un certificat de gestion (fichier limitée) à partir du fichier PFX.
- Téléchargez le fichier limitée à votre abonnement Azure.
- Convertir le fichier PFX en JKS, car Java utilise ce format s’authentifier à l’aide de certificats.
- Écrire du code de l’authentification de l’application, qui fait référence au fichier JKS local.

Lorsque vous terminez cette procédure, le certificat limitée résideront dans votre abonnement Azure et le certificat JKS résideront sur votre disque local. Pour plus d’informations sur les certificats de gestion, voir [créer et télécharger un certificat de gestion de Azure][].


#### <a name="create-a-certificate"></a>Créer un certificat

Pour créer votre propre certificat auto-signé, ouvrez une console de commande sur votre système d’exploitation et exécutez les commandes suivantes.

> **Remarque :**  L’ordinateur sur lequel vous exécutez cette commande doit avoir le JDK installé. En outre, le chemin d’accès à l’outil de clés dépend de l’emplacement dans lequel vous installez JDK. Pour plus d’informations, voir [clé et outil de gestion des certificats (keytool)][] dans la documentation en ligne Java.

Pour créer le fichier .pfx :

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Pour créer le fichier .cer :

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

Dans cet exemple :

- `<java-install-dir>`est le chemin d’accès au répertoire dans lequel vous avez installé Java.
- `<keystore-id>`est l’identificateur d’entrée combinaison de touches (par exemple, `AzureRemoteAccess`).
- `<cert-store-dir>`est le chemin d’accès au répertoire dans lequel vous souhaitez stocker les certificats (par exemple `C:/Certificates`).
- `<cert-file-name>`est le nom du fichier de certificat (par exemple `AzureWebDemoCert`).
- `<password>`le mot de passe que vous voulez protéger le certificat ; Il doit contenir au moins 6 caractères. Vous ne pouvez entrer aucun mot de passe, même si cela n’est pas recommandé.
- `<dname>`est le nom unique X.500 être associées à des alias et est utilisé comme les champs émetteur et son objet dans le certificat auto-signé.

Pour plus d’informations, voir [créer et télécharger un certificat de gestion de Azure][].


#### <a name="upload-the-certificate"></a>Télécharger le certificat

Pour télécharger un certificat auto-signé à Azure, accédez à la page **paramètres** du portail classique, puis cliquez sur l’onglet **Gestion des certificats** . Cliquez sur **Télécharger** dans la partie inférieure de la page, puis accédez à l’emplacement du fichier limitée que vous avez créé.


#### <a name="convert-the-pfx-file-into-jks"></a>Convertir le fichier PFX JKS

Dans l’invite de commandes (en cours d’exécution en tant qu’administrateur), un cd au répertoire contenant les certificats et exécutez la commande suivante, où `<java-install-dir>` est le répertoire dans lequel vous avez installé Java sur votre ordinateur :

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Lorsque vous y êtes invité, entrez le mot de passe de la combinaison de touches destination ; Il s’agit du mot de passe pour le fichier JKS.

2. Lorsque vous y êtes invité, entrez le mot de passe de combinaison de touches source ; Il s’agit le mot de passe que vous avez spécifié pour le fichier PFX.

Les deux mots de passe n’est pas ont des mêmes. Vous ne pouvez entrer aucun mot de passe, même si cela n’est pas recommandé.


## <a name="build-a-web-app-creation-application"></a>Créer une application de création d’une application Web

### <a name="create-the-eclipse-workspace-and-maven-project"></a>Créer l’espace de travail Eclipse et Maven Project

Dans cette section, vous créez un espace de travail et un projet Maven pour l’application de création de l’application web, nommée AzureWebDemo.

1. Créer un projet Maven. Cliquez sur **fichier > Nouveau > projet Maven**. Dans **Projet Maven**, sélectionnez **créer un projet simple** et un **emplacement d’espace de travail utiliser par défaut**.

2. Dans la deuxième page de **Projet Maven**, spécifiez les éléments suivants :

    - ID du groupe :`com.<username>.azure.webdemo`
    - ID d’objet : AzureWebDemo
    - Version : 0.0.1-SNAPSHOT
    - Le conditionnement : jar
    - Nom : AzureWebDemo

    Cliquez sur **Terminer**.

3. Ouvrez le nouveau fichier du projet pom.xml dans l’Explorateur de projets. Sélectionnez l’onglet **dépendances** . Comme il s’agit d’un nouveau projet, aucun package ne figurent encore.

4. Ouvrez la vue Maven référentiels. **Cliquez sur Fenêtre > Afficher la vue > autres > Maven > Maven référentiels** et cliquez sur **OK**. L’affichage de **Référentiels Maven** s’affichent dans la partie inférieure de l’IDE.

5. Ouvrez **Référentiels globaux**, avec le bouton droit le référentiel **central** , puis sélectionnez **Reconstruire l’Index**.

    ![][1]
    
    Cette étape peut prendre plusieurs minutes selon la vitesse de votre connexion. Lorsque l’index est reconstruit, vous devriez voir les packages Microsoft Azure dans le référentiel **central** Maven.

6. Dans **dépendances**, cliquez sur **Ajouter**. Dans le **Groupe entrée ID...** entrez `azure-management`. Sélectionnez les modules de gestion de la base et de gestion de l’application Service Web Apps :

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Remarque :** Si vous mettez à jour les dépendances après une nouvelle version de version, vous devez rajouter chacune des dépendances de cette liste.
    > Une fois que vous cliquez sur **Ajouter** et sélectionnez chaque dépendance, il s’affiche avec le nouveau numéro de version dans la liste des **dépendances** .

Cliquez sur **OK**. Les packages Azure puis apparaissent dans la liste des **dépendances** .


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Écrire du Code Java pour créer une application Web en appelant le Kit de développement Azure

Ensuite, écrivez le code qui appelle API dans Azure SDK pour Java pour créer l’application web application Service.

1. Créez une classe Java pour contenir le code de point d’entrée principal. Dans l’Explorateur de projets, avec le bouton droit sur le nœud de projet, puis sélectionnez **Nouveau > classe**.

2. Dans la **Nouvelle classe Java**, nommez la classe `WebCreator` et cochez la case **public principaux void statique** . Les sélections doivent apparaître comme suit :

    ![][2]

3. Cliquez sur **Terminer**. Le fichier WebCreator.java s’affiche dans l’Explorateur de projets.


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Appel de l’API Azure pour créer une application Web Application Service


#### <a name="add-necessary-imports"></a>Ajoutez des importations nécessaires

Dans WebCreator.java, ajoutez les importations ci-après ; ces importations permet d’accéder aux classes dans les bibliothèques de gestion pour l’utilisation des API Azure :

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Définir la classe de point d’entrée principal

Étant donné que l’objectif de l’application AzureWebDemo consiste à créer une application de Service Web App, nommez la classe principale pour cette application `WebAppCreator`. Ce cours fournit le code de point d’entrée principal qui appelle l’API de gestion du Service Azure pour créer l’application web.

Ajoutez les définitions de paramètres suivants pour l’application web et webspace. Vous devrez fournir vos propres informations abonnement Azure ID et le certificat.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

Dans cet exemple :

- `<subscription-id>`est l’ID de l’abonnement Azure dans lequel vous voulez créer la ressource.
- `<certificate-store-path>`est le chemin d’accès et le nom de fichier pour le fichier JKS dans votre répertoire du magasin de certificats local. Par exemple, `C:/Certificates/CertificateName.jks` pour Linux et `C:\Certificates\CertificateName.jks` pour Windows.
- `<certificate-password>`est le mot de passe que vous avez spécifié lorsque vous avez créé votre certificat JKS.
- `webAppName`peut être n’importe quel nom que vous avez choisi ; Cette procédure utilise le nom `WebDemoWebApp`. Le nom de domaine complet est le `webAppName` avec la `domainName` ajoutée, dans ce cas le domaine complet se trouve `webdemowebapp.azurewebsites.net`.
- `domainName`doit être indiquée comme indiqué ci-dessus.
- `webSpaceName`doit être une des valeurs définies dans la classe [WebSpaceNames][] .
- `appServicePlanName`doit être indiquée comme indiqué ci-dessus.

> **Remarque :** Chaque fois que vous exécutez cette application, vous devez modifier la valeur de `webAppName` et `appServicePlanName` (ou supprimer l’application web sur le portail Azure) avant l’exécution de l’application à nouveau. Dans le cas contraire, l’exécution échouera, car la même ressource existe déjà dans Azure.


#### <a name="define-the-web-creation-method"></a>Définir la méthode de création web

Ensuite, définissez une méthode pour créer l’application web. Cette méthode, `createWebApp`, spécifie les paramètres de l’application web et la webspace. Il crée et configure le client de gestion des application Service Web Apps, qui est défini par l’objet [WebSiteManagementClient][] . Le client de gestion est indispensable pour créer des applications Web. Il fournit des services web RESTful qui permettent aux applications de gérer les applications web (en effectuant les opérations telles que la création, mise à jour et supprimer) en appelant l’API de gestion de service.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Le code affiche l’état HTTP de la réponse indiquant la réussite ou l’échec et en cas de réussite, affiche le nom de l’application web créé.


#### <a name="define-the-main-method"></a>Définir la méthode main()

Fournir le code de méthode main() qui appelle createWebApp() pour créer l’application web.

Pour finir, appelez `createWebApp` de `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Exécutez l’application et vérifiez que la création de l’application web

Pour vérifier que votre application s’exécute, cliquez sur **Exécuter > exécuter**. Lorsque l’application soit terminée, vous devriez voir le résultat suivant dans la console Eclipse :

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Connectez-vous au portail classique Azure et cliquez sur **Web Apps**. La nouvelle application web doit apparaître dans la liste d’applications Web quelques minutes.


## <a name="deploying-an-application-to-the-web-app"></a>Déploiement d’une Application à l’application Web

Après avoir exécuté AzureWebDemo et créé la nouvelle application web, connectez-vous au portail classique, cliquez sur **Applications Web**, puis sélectionnez **WebDemoWebApp** dans la liste **d’Applications Web** . Dans la page de tableau de bord de l’application web, cliquez sur **Parcourir** (ou cliquez sur l’URL, `webdemowebapp.azurewebsites.net`) pour y accéder. Vous verrez une page vierge espace réservé, parce que sans contenu a été publié à l’application web encore.

Ensuite créer une application « Hello World » et le déployez dans l’application web.


### <a name="create-a-jsp-hello-world-application"></a>Créer une application Hello World JSP

#### <a name="create-the-application"></a>Créer l’application

Afin de vous montrer comment déployer une application sur le web, la procédure suivante vous montre comment créer une application « Hello World » Java simple et téléchargez-le sur l’application Service Web App votre application créée.

1. Cliquez sur **fichier > Nouveau > projet Web dynamique**. Nommez-la `JSPHello`. Vous n’avez pas besoin de modifier les autres paramètres dans cette boîte de dialogue. Cliquez sur **Terminer**.

    ![][3]

2. Dans l’Explorateur de projets, développez le projet **JSPHello** , avec le bouton droit de **contenu Web**, puis cliquez sur **Nouveau > fichier JSP**. Dans la boîte de dialogue Nouveau fichier JSP, nommez le nouveau fichier `index.jsp`. Cliquez sur **suivant**.

3. Dans la boîte de dialogue **Sélectionner un modèle de JSP** , sélectionnez **Nouveau fichier JSP (html)** , puis cliquez sur **Terminer**.

4. Dans index.jsp, ajoutez le code suivant dans la `<head>` et `<body>` balise sections :

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>Exécuter l’application Hello World dans hôte local

Avant d’exécuter cette application, vous devez configurer quelques propriétés.

1. Cliquez sur le projet **JSPHello** et sélectionnez **Propriétés**.

2. Dans la boîte de dialogue **Propriétés** : sélectionner **Le chemin d’accès de Java créer**, cliquez sur l’onglet **ordre et exporter** , vérifier **JRE système bibliothèque**, puis cliquez sur **vers le haut** pour déplacer vers le haut de la liste.

    ![][4]

3. Également dans la boîte de dialogue **Propriétés** : sélectionnez **Destinée aux services d’exécution** , cliquez sur **Nouveau**.

4. Dans la boîte de dialogue **Nouvel environnement d’exécution de serveur** , sélectionnez un serveur comme **v7.0 Apache Tomcat** et cliquez sur **suivant**. Dans la boîte de dialogue **Serveur Tomcat** , définissez le **nom** `Apache Tomcat v7.0`et définir le **Répertoire d’Installation Tomcat** à l’annuaire dans lequel vous avez installé la version de serveur Tomcat que vous souhaitez utiliser.

    ![][5]

    Cliquez sur **Terminer**.

5. Revenir à la page **Destinée aux services d’exécution** de la boîte de dialogue **Propriétés** puis. Sélectionnez **Apache Tomcat v7.0**, puis cliquez sur **OK**.

    ![][6]

6. Dans le menu Eclipse **exécuter** , cliquez sur **exécuter**. Dans la boîte de dialogue **Exécuter en tant que** , sélectionnez **exécuter sur serveur**. Dans la boîte de dialogue **exécuter sur serveur** , sélectionnez **Tomcat v7.0 Server**:

    ![][7]

    Cliquez sur **Terminer**.

7. Lorsque l’application s’exécute, vous devriez voir la page **JSPHello** s’affichent dans une fenêtre hôte local Éclipse (`http://localhost:8080/JSPHello/`), affichant le message suivant :

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>Exporter l’application comme une guerre

Exporter les fichiers de projet web sous forme d’un fichier d’archive (WAR) web afin que vous pouvez le déployez dans l’application web. Les fichiers de projet web suivants se trouvent dans le dossier de contenu Web :

    META-INF
    WEB-INF
    index.jsp

1. Cliquez sur le dossier de contenu Web et sélectionnez **Exporter**.

2. Dans la boîte de dialogue **Sélectionnez Exporter** , cliquez sur **Web > guerre** de fichier, puis cliquez sur **suivant**.

3. Dans la boîte de dialogue **Exporter guerre** , sélectionnez le répertoire src dans le projet actuel et inclure le nom du fichier WAR à la fin. Par exemple :

    `<project-path>/JSPHello/src/JSPHello.war`

Pour plus d’informations sur le déploiement de fichiers WAR, voir [Ajouter une application Java Azure Application Service Web Apps](web-sites-java-add-app.md).


### <a name="deploying-the-hello-world-application-using-ftp"></a>Déploiement de l’Application World Bonjour à l’aide de FTP

Sélectionnez un client FTP tiers pour publier l’application. Cette procédure décrit deux options : la console Kudu intégrée à Azure ; et FileZilla, un outil populaire avec une interface utilisateur pratique, graphique.

> **Remarque :** La boîte à outils Azure pour Eclipse prend en charge le déploiement aux comptes de stockage et aux services en nuage, mais ne prend pas en charge déploiement aux applications web. Vous pouvez déployer des comptes de stockage et de services de cloud à l’aide d’un projet de déploiement d’Azure comme décrit dans [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), mais pas aux applications web. Autres méthodes comme FTP ou GitHub permet de transférer des fichiers vers votre application web.

> **Remarque :** Nous ne recommandons pas par FTP à partir de l’invite de commandes (la ligne de commande FTP.EXE utilitaire fourni avec Windows). Les clients FTP qui utilisent le trafic FTP actif, par exemple FTP.EXE, souvent de ne pas fonctionner sur les pare-feu. FTP actif indique une adresse interne basés sur réseau local, à laquelle un serveur FTP risquent de ne se connecter.

Pour plus d’informations sur le déploiement à un Service d’application web app à l’aide de FTP, consultez les rubriques suivantes :

- [Déployer à l’aide d’un utilitaire FTP](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>Configurer les informations d’identification de déploiement

Vérifiez que vous avez exécuté l’application **AzureWebDemo** pour créer une application web. Vous transférerez fichiers à cet emplacement.

1. Connectez-vous au portail classique et cliquez sur **Web Apps**. Vérifiez que **WebDemoWebApp** apparaît dans la liste des applications web, puis vérifiez qu’il s’exécute. Cliquez sur **WebDemoWebApp** pour ouvrir sa page de **tableau de bord** .

2. Dans la page **tableau de bord** , sous **Aperçu rapide**, cliquez sur **configurer vos informations d’identification de déploiement** (si vous disposez déjà d’informations d’identification de déploiement, celui-ci lit **réinitialiser vos informations d’identification de déploiement**).

    Informations d’identification de déploiement sont associées à un compte Microsoft. Vous devez spécifier un nom d’utilisateur et mot de passe que vous pouvez utiliser pour déployer à l’aide de Git et FTP. Vous pouvez utiliser ces informations d’identification pour déployer une application web dans tous les abonnements Azure associé à votre compte Microsoft. Fournir des informations d’identification de la déploiement Git et FTP dans la boîte de dialogue, puis enregistrez le nom d’utilisateur et mot de passe pour une utilisation ultérieure.


#### <a name="get-ftp-connection-information"></a>Obtenir des informations de connexion FTP

Pour utiliser FTP pour déployer les fichiers d’application à l’application web nouvellement créé, vous devez obtenir des informations de connexion. Il existe deux façons d’obtenir des informations de connexion. La première consiste à visiter la page de **tableau de bord** de l’application web ; Vous pouvez est à télécharger le site web application publier le profil. Le profil de publication est un fichier XML qui fournit des informations telles que des informations d’identification nom et d’ouverture de session de hôte FTP pour vos applications web dans le Service d’application Azure. Vous pouvez utiliser ce nom d’utilisateur et mot de passe pour déployer une application web dans tous les abonnements associés au compte Azure, pas seulement celle-ci.

Pour obtenir des informations de connexion FTP à partir de la carte de l’application web dans le [Portail Azure][]:

1. Sous **Essentials**, recherchez et copiez le **nom d’hôte FTP**. Il s’agit d’un URI semblable à `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Sous **Essentials**, recherchez et **copiez/déploiement FTP nom d’utilisateur**. Cela aura la forme *webappname\deployment-username*; par exemple `WebDemoWebApp\deployer77`.

Pour obtenir des informations de connexion FTP dans le profil de publication :

1. Dans la carte de l’application web, cliquez sur **obtenir publier le profil**. Cela télécharge un fichier .publishsettings sur votre disque local.

2. Ouvrez le fichier .publishsettings dans un éditeur XML ou un éditeur de texte et recherchez le `<publishProfile>` élément contenant `publishMethod="FTP"`. Il doit ressembler à ce qui suit :

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Notez que l’application web `publishProfile` carte paramètres pour les paramètres du Gestionnaire de Site FileZilla comme suit :

- `publishUrl`est identique au **nom d’hôte FTP**, la valeur que vous définissez dans **hôte**.
- `publishMethod="FTP"`signifie que vous la valeur **protocole** **FTP - File Transfer Protocol**et le **chiffrement** pour **utiliser FTP simple**.
- `userName`et `userPWD` sont des clés pour le nom d’utilisateur et les valeurs de mot de passe que vous avez spécifié lorsque vous effectuez les informations d’identification de déploiement. `userName`est identique à **déploiement / FTP utilisateur**. Elles sont mappées vers **utilisateur** et **mot de passe** dans FileZilla.
- `ftpPassiveMode="True"`signifie que le site FTP utilise transfert FTP passif ; Sélectionnez **passif** sous l’onglet **Paramètres de transfert** .


#### <a name="configure-the-web-app-to-host-a-java-application"></a>Configurer l’application Web pour héberger une application Java

Avant de publier l’application, vous devez modifier certains paramètres de configuration afin que l’application web peut héberger une application Java.

1. Dans le portail classique, accédez à la page de **tableau de bord** de l’application web et cliquez sur **configurer**. Dans la page **configurer** , spécifiez les paramètres suivants.

2. Dans la **version Java** la valeur par défaut est **désactivé**; Sélectionnez la version Java votre application cible ; par exemple 1.7.0_51. Après cela, assurez-vous également que **conteneur Web** est défini sur une version de serveur Tomcat.

3. Dans les **Documents par défaut**, ajoutez index.jsp et déplacer vers le haut de la liste. (Le fichier par défaut pour les applications web est hostingstart.html).

4. Cliquez sur **Enregistrer**.


#### <a name="publish-your-application-using-kudu"></a>Publiez votre application à l’aide de Kudu

Un moyen de publier l’application consiste à utiliser la console de débogage Kudu intégrée à Azure. Kudu est connue pour être stable et cohérente avec application de Service Web Apps et Tomcat Server. Pour accéder à la console pour l’application web en accédant à une URL de la forme suivante :

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Pour cette procédure, la console Kudu se trouve à l’adresse suivante ; Accédez à cet emplacement :

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. Dans le menu supérieur, sélectionnez **Console de débogage > CMD**.

3. Dans la ligne de commande console, accédez à `/site/wwwroot` (ou cliquez sur `site`, puis `wwwroot` dans l’affichage du répertoire en haut de la page) :

    `cd /site/wwwroot`

4. Une fois que vous spécifiez **version Java**, serveur Tomcat doit créer un répertoire d’applications Web. Dans la ligne de commande console, accédez au répertoire d’applications Web :

    `mkdir webapps`

    `cd webapps`

5. Faites glisser JSPHello.war à partir de `<project-path>/JSPHello/src/` et déposez-le dans la vue de répertoire Kudu sous `/site/wwwroot/webapps`. Pas faisant glisser vers la zone « Faites glisser ici pour télécharger et le code postal », car il Tomcat le décompressez.

  ![][8]

En premier JSPHello.war apparaît dans la zone répertoire par lui-même :

  ![][9]

En peu de temps (probablement moins de 5 minutes) serveur Tomcat sera décompresser le fichier WAR dans un répertoire JSPHello décompressé. Cliquez sur le répertoire racine pour voir si index.jsp a été décompressés et y copiés. Si c’est le cas, accédez au répertoire d’applications Web pour voir si le répertoire JSPHello décompressé a été créé. Si vous ne voyez pas ces éléments, attendez et répétez.

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>Publiez votre application à l’aide de FileZilla (facultatif)

FileZilla, un client FTP tiers populaires avec une interface utilisateur pratique, le graphique est un autre outil que vous pouvez utiliser pour publier l’application. Vous pouvez télécharger et installer FileZilla à partir de [http://filezilla-project.org/](http://filezilla-project.org/) si vous n’en avez pas déjà dessus. Pour plus d’informations sur l’utilisation du client, voir la [documentation FileZilla](https://wiki.filezilla-project.org/Documentation) et cette entrée de blog sur [Clients FTP - partie 4 : FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. Dans FileZilla, cliquez sur **fichier > Gestionnaire de Site**.
2. Dans la boîte de dialogue **Gestionnaire de Site** , cliquez sur **Nouveau Site**. Un nouveau site FTP vierge s’affichent dans **Sélectionner une entrée de** vous inviter à fournir un nom. Pour cette procédure, nommez-la `AzureWebDemo-FTP`.

    Sous l’onglet **Général** , spécifiez les paramètres suivants :
    - **Hôte :** Entrez le **Nom d’hôte FTP** que vous avez copiée à partir du tableau de bord.
    - **Port :** (Laissez ce vide, qu’il s’agit d’un transfert passif et le serveur déterminera le port à utiliser).
    - **Protocole :** Protocole de transfert de fichier FTP
    - **Chiffrement :** Utiliser le mode FTP simple
    - **Type d’ouverture de session :** Normal
    - **Utilisateur :** Entrez le déploiement / FTP utilisateur que vous avez copiée à partir du tableau de bord. Il s’agit du nom d’utilisateur FTP complète, c'est-à-dire le formulaire *webappname\username*.
    - **Mot de passe :** Entrez le mot de passe que vous avez spécifié lorsque vous définissez les informations d’identification de déploiement.

    Sous l’onglet **Paramètres de transfert** , sélectionnez **Passive**.

3. Cliquez sur **se connecter**. Si la réussite, console de FileZilla affiche un `Status: Connected` message et problème un `LIST` commande pour afficher le contenu du répertoire.

4. Dans le panneau de configuration de site **Local** , sélectionnez le répertoire source dans lequel se trouve le fichier JSPHello.war ; le chemin d’accès sera semblable à ce qui suit :

    `<project-path>/JSPHello/src/`

5. Dans le panneau de site **à distance** , sélectionnez le dossier de destination. Vous déployez le fichier WAR dans le `webapps` répertoire sous la racine de l’application web. Accédez à `/site/wwwroot`, avec le bouton droit sur `wwwroot`, puis sélectionnez **créer le répertoire**. Nommez le répertoire `webapps` , entrez ce répertoire.

6. Transférer JSPHello.war à `/site/wwwroot/webapps`. Sélectionnez JSPHello.war dans la liste de fichiers **Local** , avec le bouton droit dessus, puis sélectionnez **Télécharger**. Vous supposés les voir apparaître dans `/site/wwwroot/webapps`.

7. Une fois que vous copiez JSPHello.war dans le répertoire d’applications Web, serveur Tomcat sera automatiquement décompressez (décompresser) les fichiers dans le fichier WAR. Bien que le serveur Tomcat commence décompression presque immédiatement, il peut prendre un long délai (éventuellement heures) pour les fichiers s’affichent dans le client FTP.


#### <a name="run-the-hello-world-application-on-the-web-app"></a>Exécutez l’application Hello World sur l’application Web

1. Après avoir téléchargé le fichier WAR et vérifié que Tomcat serveur a créé un décompressés `JSPHello` répertoire, accédez à `http://webdemowebapp.azurewebsites.net/JSPHello` pour exécuter l’application.

    > **Remarque :** Si vous cliquez sur **Parcourir** à partir du portail classique, vous pouvez obtenir la page Web par défaut, indiquant « cette application web basée sur Java a été créée. » Vous devrez peut-être actualiser la page Web pour afficher le résultat de l’application à la place de la page Web par défaut.

2. Lorsque l’application s’exécute, vous devriez voir une page web avec le résultat suivant :

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>Nettoyer les ressources Azure

Cette procédure crée une application web application Service. Vous serez facturé pour la ressource dans la mesure où il existe. Sauf si vous envisagez de continuer à utiliser l’application web de test ou de développement, vous devez prendre en compte arrêter ou de le supprimer. Une application web qui a été arrêtée est toujours facturés petite, mais vous pouvez le redémarrer à tout moment. Supprimer une application web efface toutes les données que vous avez téléchargées à celui-ci.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Service application Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Programme d’installation de la plateforme Web]: http://go.microsoft.com/fwlink/?LinkID=252838
[Kit de ressources Azure pour Éclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Portail classique Azure]: https://manage.windowsazure.com
[Qu’est un répertoire Azure AD]: http://technet.microsoft.com/library/jj573650.aspx
[Créer et télécharger un certificat de gestion d’Azure]: ../cloud-services/cloud-services-certs-create.md
[Clé et outil de gestion des certificats (keytool)]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Portail Azure]: https://portal.azure.com
