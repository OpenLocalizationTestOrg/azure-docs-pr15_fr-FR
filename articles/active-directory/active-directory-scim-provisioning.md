<properties
    pageTitle="À l’aide de SCIM pour activer la configuration automatique d’utilisateurs et groupes d’Azure Active Directory aux applications | Microsoft Azure"
    description="Azure Active Directory peut configurer automatiquement les utilisateurs et groupes dans le magasin d’application ou l’identité qui est fronted par un service Web avec l’interface définie dans la spécification du protocole SCIM"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>À l’aide de SCIM pour activer la configuration automatique d’utilisateurs et groupes d’Azure Active Directory aux applications

##<a name="overview"></a>Vue d’ensemble

Azure Active Directory pouvez automatiquement configurer les utilisateurs et groupes dans le magasin d’application ou l’identité qui est fronted par un service Web avec l’interface défini dans la [spécification du protocole SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory peut envoyer des demandes pour créer, modifier et supprimer les utilisateurs et groupes pour ce service Web, qui peuvent traduire ces demandes en opérations lors de la banque d’identités cible. 

![][1]
*Figure : Mise en service à partir d’Azure Active Directory pour une banque d’identités via un service Web*

Cette fonctionnalité peut être utilisée conjointement avec la fonctionnalité «[Afficher par votre propre application](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)» dans Azure AD pour activer l’authentification unique et mise en service pour les applications qui fournissent ou fronted par un service web SCIM automatique de l’utilisateur.

Il existe deux cas d’utilisation pour SCIM dans Azure Active Directory :

* **Mise en service des utilisateurs et groupes pour les applications qui prennent en charge SCIM** - Applications qui prennent en charge SCIM 2.0 et utiliser les jetons PORTEUR OAuth pour l’authentification fonctionnera avec Azure Active Directory de la zone.

* **Créer votre propre solution mise en service pour les applications qui prennent en charge d’autres en fonction de l’API de mise en service** - non SCIM applications, vous pouvez créer un point de terminaison SCIM traduire entre du Azure AD point de terminaison SCIM et tout ce qui prend en charge l’API de l’application de mise en service de l’utilisateur.  Pour faciliter le développement d’un point de terminaison SCIM, nous fournissons des bibliothèques d’infrastructure du langage commun ainsi que des exemples de code qui vous montrent comment fournir un point de terminaison SCIM et convertir des messages SCIM.  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Mise en service des utilisateurs et groupes pour les Applications qui prennent en charge SCIM

Azure Active Directory peut être configuré pour les utilisateurs confié offre automatiquement et groupes aux applications qui mise en œuvre d’un site Web de [système de gestion des identités domaines 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) et acceptent des jetons de porteur OAuth pour l’authentification. Dans la spécification 2.0 SCIM, applications doivent respecter ces règles :

* Prend en charge la création d’utilisateurs et/ou groupes, suivant le point 3.3 du protocole SCIM.  

* Prend en charge la modification des utilisateurs et/ou groupes avec des demandes de correctif suivant le point 3.5.2 du protocole SCIM.  

* Prend en charge la récupération d’une ressource connue suivant le point 3.4.1 du protocole SCIM.  

*  Prend en charge l’interrogation utilisateurs et/ou groupes, suivant le point 3.4.2 du protocole SCIM.  Par défaut, les utilisateurs sont interrogées par externalId et groupes sont interrogées par nom complet.  

* Prend en charge interroger à l’utilisateur par ID et par gestionnaire suivant le point 3.4.2 du protocole SCIM.  

* Prend en charge l’interrogation de groupes par ID et par membre suivant le point 3.4.2 du protocole SCIM.  

* Accepte des jetons de porteur OAuth d’autorisation suivant le point 2.1 du protocole SCIM.

Vous devez vérifier avec votre fournisseur d’application, ou la documentation de votre fournisseur d’application pour les instructions de compatibilité à ces exigences.
 
###<a name="getting-started"></a>Prise en main

Applications prenant en charge le profil SCIM ci-dessus pouvant être connectées à Azure Active Directory à l’aide de la fonctionnalité de l’application « personnalisé » dans la galerie des applications Azure AD. Une fois connecté, Azure AD s’exécute un processus de synchronisation 5 minutes où il point de terminaison de l’application SCIM pour les utilisateurs et groupes, des requêtes et crée ou modifie les selon les détails de l’affectation.

**Pour vous connecter à une application qui prend en charge SCIM :**

1.  Dans un navigateur web, lancez le portail de gestion Azure en https://manage.windowsazure.com.
2.  Accédez à **Active Directory > répertoire > [votre répertoire] > Applications**, puis sélectionnez **Ajouter > Ajouter une application à partir de la galerie**.
3.  Sélectionnez l’onglet **personnalisées** à gauche, entrez un nom pour votre application, puis cliquez sur l’icône de coche pour créer un objet application.

![][2]

4.  Dans l’écran, sélectionnez le deuxième bouton **configurer mise en service de compte** .
5.  Dans le champ **URL du point de terminaison de mise en service** , entrez l’URL du point de terminaison de l’application SCIM.
6.  Si le point de terminaison SCIM requiert un jeton PORTEUR OAuth à partir d’un émetteur différent Azure AD, puis copiez le jeton PORTEUR OAuth requis dans le champ **Jeton d’authentification (facultatif)** . Est ce champ est vide, puis Azure AD inclura un jeton PORTEUR OAuth émis à partir d’Azure AD avec chaque requête. Applications qui utilisent Azure AD comme un fournisseur idenity pouvez valider ce Azure AD-émis jeton.
7.  Cliquez sur **suivant**, puis cliquez sur le bouton **Démarrer le Test** d’avoir Azure Active Directory essaie de se connecter au point de terminaison SCIM. Si les tentatives échouent, des informations de diagnostic seront affichera.  
8.  Si le tente de se connecter à la réussir application, puis cliquez sur **suivant** dans les autres écrans, puis cliquez sur **Terminer** pour quitter la boîte de dialogue.
9.  Dans l’écran, sélectionnez le bouton **Comptes affecter** tiers. Dans la section utilisateurs et groupes qui en résulte, attribuer les utilisateurs ou groupes qu'auxquels mise en service à l’application.
10. Une fois que les utilisateurs et groupes affectés, cliquez sur l’onglet **configurer** dans la partie supérieure de l’écran.
11. Sous **Mise en service de compte**, vérifiez que le statut est défini sur. 
12. Sous **Outils**, cliquez sur **redémarrer la mise en service de compte** pour démarrer le processus de configuration.

Notez que 5 à 10 minutes peut s’écouler avant d’envoyer des demandes au point de terminaison SCIM commence le processus de configuration.  Un résumé des tentatives de connexion est fourni sous l’onglet tableau de bord de l’application, et un rapport d’activité de mise en service et des erreurs de mise en service peuvent être téléchargés à partir de l’onglet Rapports de l’annuaire.

##<a name="building-your-own-provisioning-solution-for-any-application"></a>Création de votre propre Solution de mise en service pour n’importe quelle Application

En créant un service web SCIM qui interagit avec Azure Active Directory, vous pouvez activer seul utilisateur authentification et automatique mis en service pour pratiquement n’importe quelle application qui fournit un utilisateur reste ou SOAP API de mise en service.

Voici comment procéder :

1.  Azure AD fournit une bibliothèque d’infrastructure langue courantes nommée [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Les développeurs et les intégrateurs peuvent utiliser cette bibliothèque pour créer et déployer un point de terminaison web basées sur les SCIM service capable de se connecter Azure AD au magasin des identités de n’importe quelle application.
2.  Mappages sont implémentées dans le service web pour établir une correspondance entre le schéma utilisateur normalisé le schéma utilisateur et le protocole requises par l’application.
3.  L’URL du point de terminaison est enregistré dans Azure AD dans le cadre d’une application personnalisée dans la galerie d’applications.
4.  Utilisateurs et groupes affectés à cette application dans Azure Active Directory. Lors de l’affectation, ils sont placés dans une file d’attente à synchroniser dans l’application cible. Le processus de synchronisation gère la file d’attente s’exécute toutes les 5 minutes.

###<a name="code-samples"></a>Exemples de code

Pour simplifier ce processus, un ensemble [d’exemples de code](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) sont fournis que créer un point de terminaison du service web SCIM et montrez la mise à disponibilité automatique. Un échantillon est d’un fournisseur qui met à jour un fichier avec des lignes de valeurs séparées par des virgules représentant des utilisateurs et groupes.  L’autre est d’un fournisseur qui s’exécute sur le service Amazon Web Services de gestion des identités et Access.  

**Conditions préalables**

* Visual Studio 2013 ou version ultérieure
* [Kit de développement Azure pour .NET](https://azure.microsoft.com/downloads/)
* Ordinateur Windows qui prend en charge l’infrastructure ASP.NET 4.5 à utiliser comme point de terminaison SCIM. Cet ordinateur doit être accessible à partir du cloud
* [Un abonnement Azure avec une version d’évaluation ou sous licence d’Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* L’exemple Amazon AWS requiert des bibliothèques à partir de la [Boîte à outils AWS pour Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Consultez le fichier Lisez-moi inclus dans l’échantillon pour plus d’informations

###<a name="getting-started"></a>Prise en main

Pour mettre en œuvre d’un point de terminaison SCIM peut accepter des demandes de mise en service à partir d’Azure AD, le plus simple consiste à créer et déployer l’exemple de code qui renvoie les utilisateurs générés à un fichier de valeurs séparées par des virgules (CSV).

**Pour créer un point de terminaison SCIM exemple :**

1.  Télécharger le package d’exemples de code en [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Décompressez le package et placez-le sur votre ordinateur Windows à un emplacement tel que C:\AzureAD-BYOA-Provisioning-Samples\.
3.  Dans ce dossier, lancez la solution FileProvisioningAgent dans Visual Studio.
4.  Sélectionnez **Outils > Gestionnaire de Package de bibliothèque > Gestionnaire de Package Console**et exécutez les commandes ci-dessous pour le projet FileProvisioningAgent pour résoudre les références de solution :

    Package d’installation Package d’installation Microsoft.SystemForCrossDomainIdentityManagement Package d’installation Microsoft.IdentityModel.Clients.ActiveDirectory Package d’installation Microsoft.Owin.Diagnostics Microsoft.Owin.Host.SystemWeb

5.  Générez le projet FileProvisioningAgent.
6.  Lancez l’application invite de commandes dans Windows (en tant qu’administrateur) et utilisez la commande **cd** pour changer le répertoire vers votre dossier **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** .
7.  Exécutez la commande ci-dessous, en remplaçant < adresse ip > par le nom de domaine ou des adresses IP de l’ordinateur Windows.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  Dans Windows sous **Paramètres Windows > Paramètres d’Internet et réseau**, sélectionnez le **pare-feu Windows > Paramètres avancés**et créez une **Règle de trafic entrant** qui autorise l’accès entrant au port 9000.
9.  Si l’ordinateur Windows est derrière un routeur, le routeur devront être configurés pour effectuer la traduction d’accès réseau entre son port 9000 qui est exposé à internet et le port 9000 sur l’ordinateur Windows. Cela est nécessaire pour Azure AD pouvoir accéder à ce point de terminaison dans le cloud.


**Pour enregistrer le point de terminaison SCIM exemple dans Azure Active Directory :**

1.  Dans un navigateur web, lancez le portail de gestion Azure en https://manage.windowsazure.com.
2.  Accédez à **Active Directory > répertoire > [votre répertoire] > Applications**, puis sélectionnez **Ajouter > Ajouter une application à partir de la galerie**.
3.  Sélectionnez l’onglet **personnalisées** à gauche, entrez un nom tel que « SCIM Test application », puis cliquez sur l’icône de coche pour créer un objet application. Notez que l’objet application créé est l’intention pour représenter l’application cible vous être mise en service pour et mise en œuvre de l’authentification unique pour et pas seulement le point de terminaison SCIM.

![][2]

4.  Dans l’écran, sélectionnez le deuxième bouton **configurer mise en service de compte** .
5.  Dans la boîte de dialogue, entrez l’URL et le port de votre point de terminaison SCIM exposées à internet. Il s’agit quelque chose comme http://testmachine.contoso.com:9000 ou http://<ip-address>:9000/, où < adresse ip > est internet exposé IP adresse.  
6.  Cliquez sur **suivant**, puis cliquez sur le bouton **Démarrer le Test** d’avoir Azure Active Directory essaie de se connecter au point de terminaison SCIM. Si les tentatives échouent, des informations de diagnostic seront affichera.  
7.  En cas d’échec de la tentative de connexion à votre service Web, puis cliquez sur **suivant** à l’écran, puis cliquez sur **Terminer** pour quitter la boîte de dialogue.
8.  Dans l’écran, sélectionnez le bouton **Comptes affecter** tiers. Dans la section utilisateurs et groupes qui en résulte, attribuer les utilisateurs ou groupes qu'auxquels mise en service à l’application.
9.  Une fois que les utilisateurs et groupes affectés, cliquez sur l’onglet **configurer** dans la partie supérieure de l’écran.
10. Sous **Mise en service de compte**, vérifiez que le statut est défini sur. 
11. Sous **Outils**, cliquez sur **redémarrer la mise en service de compte** pour démarrer le processus de configuration.

Notez que 5 à 10 minutes peut s’écouler avant d’envoyer des demandes au point de terminaison SCIM commence le processus de configuration.  Un résumé des tentatives de connexion est fourni sous l’onglet tableau de bord de l’application, et un rapport d’activité de mise en service et des erreurs de mise en service peuvent être téléchargés à partir de l’onglet Rapports de l’annuaire.

La dernière étape de vérification de l’échantillon consiste à ouvrir le fichier TargetFile.csv dans le dossier \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug sur votre ordinateur Windows. Une fois le processus de configuration est exécuté, ce fichier affiche les détails de l’ensemble affectées et sa mise en service des utilisateurs et groupes.

###<a name="development-libraries"></a>Bibliothèques de développement

Pour développer votre propre service Web qui est conforme à la spécification SCIM, tout d’abord vous familiariser avec les bibliothèques suivantes fournis par Microsoft pour aider à accélérer le processus de développement : 

**1:**  Bibliothèques de langue Infrastructure communes sont proposées pour une utilisation avec les langages basés sur cette infrastructure, tels que c#.  Une de ces bibliothèques, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), déclare une interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, montrée dans l’illustration ci-dessous.  Un développeur qui utilise les bibliothèques serait implémenter cette interface avec une classe qui peut être désignée, générique, en tant que fournisseur.  Les bibliothèques autorisez le développeur à déployer facilement un service Web qui est conforme à la spécification SCIM, soit hébergé dans Internet Information Services, ou n’importe quel exécutable assembly Infrastructure du langage commun.  Demandes de service Web doivent être convertis en appels aux méthodes du fournisseur, qui seraient être programmés par le développeur de fonctionner sur certains magasin identité.    

![][3]

**2:** [Gestionnaires d’itinéraire Express](http://expressjs.com/guide/routing.html) sont disponibles pour l’analyse des objets de requête node.js représentant des appels (tel que défini par la spécification SCIM), apportées à un service Web node.js.     

###<a name="building-a-custom-scim-endpoint"></a>Création d’un point de terminaison SCIM personnalisé

En utilisant les bibliothèques décrits ci-dessus, les développeurs à l’aide de ces bibliothèques peuvent héberger leurs services dans un assembly Common Language Infrastructure exécutable ou au sein de services Internet (IIS).  Voici des exemples de code pour l’hébergement d’un service au sein d’un assembly exécutable, à l’adresse http://localhost : 9000 : 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Il est important de noter que ce service doit avoir un HTTP adresse et serveur de certificat d’authentification dont l’autorité de certification racine est une des opérations suivantes : 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Certificat d’authentification serveur peut être lié à un port sur un hôte Windows à l’aide de l’utilitaire de shell réseau, comme suit : 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
Ici, la valeur fournie pour l’argument certhash représente l’empreinte numérique du certificat, tandis que la valeur fournie pour l’argument identificateur est un identificateur unique global arbitraire.  

Pour héberger le service au sein de services Internet (IIS), un développeur serait créer un assembly de bibliothèque de code Common Language Infrastructure avec une classe nommée démarrage dans l’espace de noms par défaut de l’assembly.  Voici un exemple d’une telle classe : 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>Gère l’authentification de point de terminaison

Les demandes d’Azure Active Directory incluent un jeton PORTEUR OAuth 2.0.   N’importe quel service reçoit la requête doit authentifier l’émetteur comme étant Azure Active Directory au nom du client Azure Active Directory attendu, pour accéder au service de graphique Web Azure d’Active Directory.  Dans le jeton, l’émetteur est identifié par un réclamer iss, tels que « iss » : « https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ».  Dans cet exemple, l’adresse de base de la valeur réclamer, https://sts.windows.net, identifie Azure Active Directory en tant que l’émetteur, tandis que le segment adresse relative, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, est un identificateur unique du client Azure Active Directory pour le compte duquel le jeton a été émis.  Si le jeton a été émis pour accéder à graphique Web service du Azure Active Directory, l’identificateur de ce service, 00000002-0000-0000-c000-type "000000000000", doit se trouver dans la valeur de réclamer d’aud du jeton.  

Les développeurs qui utilisent les bibliothèques Common Language Infrastructure fournies par Microsoft pour la création d’un service SCIM peuvent authentifier les demandes d’Azure Active Directory à l’aide du package Microsoft.Owin.Security.ActiveDirectory en procédant comme suit : 

**1:**  Dans un fournisseur, implémentez la propriété Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior en lui renvoyer une méthode à appeler à chaque fois que le service est démarré : 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  Ajoutez le code suivant à cette méthode d’avoir toute demande à un des points de terminaison du service authentifiés comme portant un jeton émis par Azure Active Directory au nom d’un client spécifié, pour accéder au service de graphique Web Azure d’Active Directory : 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>Utilisateur et le schéma de groupe

Azure Active Directory pouvez prévoir deux types de ressources pour les Services Web SCIM.  Ces types de ressources sont des utilisateurs et groupes.  

Ressources de l’utilisateur sont identifiés par l’identificateur de schéma, urn : ietf:params:scim:schemas:extension:enterprise:2.0:User, qui est incluse dans cette spécification du protocole : http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Le mappage par défaut des attributs d’utilisateurs dans Azure Active Directory avec les attributs de ressources urn : ietf:params:scim:schemas:extension:enterprise:2.0:User est fourni dans la table 1, ci-dessous.  

Regrouper les ressources identifiées par l’identificateur de schéma, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tableau 2, ci-dessous, indique le mappage par défaut des attributs de groupes dans Azure Active Directory pour les attributs de ressources http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

###<a name="table-1-default-user-attribute-mapping"></a>Tableau 1 : Mappage des attributs utilisateur par défaut

| Azure utilisateur Active Directory | urn : ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | Active |
| nom complet | nom complet |
| Télécopie-numéro de téléphone | valeur phoneNumbers [eq de type « télécopie »] |
| Prénom | name.givenName |
| jobTitle | titre |
| courrier | valeur de messages électroniques [eq de type « travail »] |
| mailNickname | externalId |
| Gestionnaire | Gestionnaire |
| Mobile | valeur phoneNumbers [eq de type « mobile »] |
| objectId | ID |
| code postal | adresses [eq de type « travail »] .postalCode |
| Adresses de proxy | messages électroniques [Tapez eq « autre »]. Valeur |
| physique-remise-OfficeName | adresses [Tapez eq « autre »]. Mise en forme |
| streetAddress | adresses [eq de type « travail »] .streetAddress |
| nom de famille | name.familyName |
| Numéro de téléphone | valeur phoneNumbers [eq de type « travail »] |
| au PrincipalName utilisateur | nom d’utilisateur |


###<a name="table-2-default-group-attribute-mapping"></a>Tableau 2 : Mappage des attributs de groupe par défaut

| Groupe Active Directory Azure | http://schemas.Microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| nom complet | externalId |
| courrier | valeur de messages électroniques [eq de type « travail »] |
| mailNickname | nom complet |
| membres | membres |
| objectId | ID |
| proxyAddresses | messages électroniques [Tapez eq « autre »]. Valeur |


##<a name="user-provisioning-and-de-provisioning"></a>Configuration des utilisateurs et désactivation des comptes

La figure suivante montre les messages qu’Azure Active Directory vous envoie à un service SCIM pour gérer le cycle de vie d’un utilisateur dans une autre identité stockent.  Le diagramme montre également comment un service SCIM implémenté en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la création de ces services sera-t-il organisé ces demandes en appels vers les méthodes d’un fournisseur.  

![][4]
*Figure : Configuration des utilisateurs et désactivation des comptes séquence*

**1:**  Azure Active Directory interrogent le service pour un utilisateur avec une valeur de l’attribut externalId correspondant à la valeur de l’attribut mailNickname d’un utilisateur dans Azure Active Directory.  La requête est exprimée sous forme de demande Hypertext Transfer Protocol comme celui-ci, où jyoung est un exemple d’un mailNickname d’un utilisateur dans Azure Active Directory : 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Si le service a été créé en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour implémenter des services de SCIM, la demande doivent être convertie en un appel à la méthode de requête de fournisseur de service.  Voici la signature de cette méthode : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Voici la définition de l’interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters : 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

Dans le cas de l’exemple précédent d’une requête pour un utilisateur avec une valeur indiquée pour l’attribut externalId, valeurs des arguments passés à la méthode de requête sera comme suit : 

* paramètres. AlternateFilters.Count : 1
* paramètres. AlternateFilters.ElementAt(0). AttributePath : « externalId »
* paramètres. AlternateFilters.ElementAt(0). OpérateurComparaison : ComparisonOperator.Equals
* paramètres. AlternateFilter.ElementAt(0). ComparisonValue : « jyoung »
* correlationIdentifier : System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. ID de demande »] 

**2:**  Si la réponse à une requête pour le service d’un utilisateur avec une valeur de l’attribut externalId correspondant à la valeur de l’attribut mailNickname d’un utilisateur dans Active Directory Azure ne renvoie pas tous les utilisateurs, Azure Active Directory demander que le service de mise en service un utilisateur correspondant à celui d’Azure Active Directory.  Voici un exemple d’une telle demande : 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Les bibliothèques Common Language Infrastructure fournis par Microsoft pour implémenter des services de SCIM seraient traduire cette requête en un appel à la méthode Create de fournisseur de service.  La méthode Create a cette signature : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

Dans le cas d’une demande de configurer un utilisateur, la valeur de l’argument ressource sera une instance de le Microsoft.SystemForCrossDomainIdentityManagement. Cours Core2EnterpriseUser, définis dans la bibliothèque Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Si la demande de mise en service de l’utilisateur a réussi, puis l’implémentation de la méthode est censée renvoyer une instance de la la Microsoft.SystemForCrossDomainIdentityManagement. Classe Core2EnterpriseUser, avec la valeur de la propriété identificateur définie à l’identificateur unique de l’utilisateur que vous venez mis en service.  

**3:**  Pour mettre à jour un utilisateur connu pour être présent dans un magasin d’identité fronted par un SCIM, Azure Active Directory effectuera en demandant l’état actuel de cet utilisateur à partir du service avec une demande comme celui-ci : 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

Dans un service basé en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour implémenter des services de SCIM, la demande doivent être convertie en un appel à la méthode de récupération de fournisseur de service.  Voici la signature de la méthode récupérer : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

Dans le cas de l’exemple précédent d’une requête d’extraction de l’état actuel d’un utilisateur, les valeurs des propriétés de l’objet fourni sous forme de la valeur de l’argument parameters seront comme suit : 

* Identificateur : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »

**4:**  Si un attribut de référence doit être mis à jour, puis Azure Active Directory interrogent le service pour déterminer si la valeur actuelle de l’attribut de référence dans le magasin d’identités fronted par le service déjà correspond à la valeur de l’attribut dans Azure Active Directory.  Dans le cas d’utilisateurs, le seul attribut dont la valeur actuelle sera interrogée de cette façon est l’attribut gestionnaire.  Voici un exemple d’une requête pour déterminer si l’attribut Gestionnaire d’un objet particulier utilisateur possède actuellement une certaine valeur : 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

La valeur du paramètre de requête attributs, id, signifie que s’il existe à un objet utilisateur qui correspond à l’expression fournie sous forme de la valeur du paramètre de requête filtre, puis le service est censé répondre avec une ressource urn : ietf:params:scim:schemas:core:2.0:User ou urn : ietf:params:scim:schemas:extension:enterprise:2.0:User, y compris uniquement la valeur de l’attribut id de cette ressource.  Bien entendu, la valeur de l’attribut id est connue au demandeur, il est inclus dans la valeur du paramètre de requête filtre ; l’objectif de demande pour qu’elle est en réalité pour demander une représentation minimale d’une ressource qui répondant à l’expression de filtre en tant qu’une indication d’ou non ce n’importe quel objet existe.   

Si le service a été créé en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour implémenter des services de SCIM, la demande doivent être convertie en un appel à la méthode de requête de fournisseur de service.  La valeur des propriétés de l’objet fourni sous forme de la valeur de l’argument parameters sera comme suit : 

* paramètres. AlternateFilters.Count : 2
* paramètres. AlternateFilters.ElementAt(x). AttributePath : « id »
* paramètres. AlternateFilters.ElementAt(x). OpérateurComparaison : ComparisonOperator.Equals
* paramètres. AlternateFilter.ElementAt(x). ComparisonValue : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* paramètres. AlternateFilters.ElementAt(y). AttributePath : « Gestionnaire »
* paramètres. AlternateFilters.ElementAt(y). OpérateurComparaison : ComparisonOperator.Equals
* paramètres. AlternateFilter.ElementAt(y). ComparisonValue : « 2819c223-7f76-453a-919d-413861904646 »
* paramètres. RequestedAttributePaths.ElementAt(0) : « id »
* paramètres. SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »

Dans ce cas, la valeur de l’index x peut être 0 et la valeur de la valeur y index peut-être être 1, ou la valeur de x peut être 1 et la valeur de y peut être 0, selon l’ordre des expressions du paramètre de requête de filtre.   

**5:**  Voici un exemple d’une requête à partir d’Azure Active Directory à un service SCIM pour mettre à jour un utilisateur : 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

Les bibliothèques Microsoft Common Language Infrastructure d’implémentation des services SCIM seraient traduire la demande dans un appel à la méthode de mise à jour du fournisseur de service.  Voici la signature de cette méthode : 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



Dans le cas de l’exemple précédent d’une requête pour mettre à jour un utilisateur, l’objet fourni sous forme de la valeur de l’argument correctif aura ces valeurs de propriété : 

* ResourceIdentifier.Identifier : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* ResourceIdentifier.SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »
* (PatchRequest en tant que PatchRequest2). Operations.Count : 1
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). NomOpération : OperationName.Add
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Path.AttributePath : « Gestionnaire »
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Value.Count : 1
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Référence : http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valeur : 2819c223-7f76-453a-919d-413861904646

**6:**  Pour mettre hors service un utilisateur travaillant dans un magasin d’identité fronted par un service SCIM, Azure Active Directory vous envoie une demande comme celui-ci : 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Si le service a été créé en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour implémenter des services de SCIM, la demande doivent être convertie en un appel à la méthode Delete de fournisseur de service.   Cette méthode a cette signature : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
L’objet fourni sous forme de la valeur de l’argument resourceIdentifier aura ces valeurs de propriétés dans le cas de l’exemple précédent d’une requête pour mettre hors service un utilisateur : 

* ResourceIdentifier.Identifier : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* ResourceIdentifier.SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »

##<a name="group-provisioning-and-de-provisioning"></a>Groupe mise en service et la désactivation des comptes

La figure suivante montre les messages qu’Azure Active Directory vous envoie à un service SCIM pour gérer le cycle de vie d’un groupe dans une autre identité stockent.  Ces messages diffèrent des messages relatifs aux utilisateurs de trois manières : 

* Le schéma d’une ressource de groupe est traité comme http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Requêtes pour retrouver les groupes déterminera que l’attribut membres doit être exclues à partir de n’importe quelle ressource fournie en réponse à la demande.  
* Demandes pour déterminer si un attribut de référence a une certaine valeur sera demandes relatives à l’attribut membres.  

![][5]
*Figure : Mise en service de groupe et désactivation des comptes séquence*

##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser utilisateur mise en service / d’annulation aux applications SaaS](active-directory-saas-app-provisioning.md)
- [Personnalisation des mappages d’attributs de mise en service de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Étendue de filtres de mise en service de l’utilisateur](active-directory-saas-scoping-filters.md)
- [Notifications de mise en service du compte](active-directory-saas-account-provisioning-notifications.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
