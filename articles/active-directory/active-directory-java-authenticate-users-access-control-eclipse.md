<properties
    pageTitle="Comment utiliser le contrôle d’accès (Java) | Microsoft Azure"
    description="Apprenez à développer et utiliser un contrôle d’accès avec Java dans Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>L’authentification des utilisateurs Web avec le Service de contrôle d’accès Azure à l’aide d’Éclipse

Ce guide vous montrent comment utiliser le Service de contrôle Access Azure (ACS) dans la boîte à outils Azure pour Eclipse. Pour plus d’informations sur ACS, consultez la section [étapes suivantes](#next_steps) .

> [AZURE.NOTE]
> Le filtre de contrôle Azure Access Services est un aperçu de la technologie Communauté. En tant que logiciel précommercial, il n'est pas anciennement pris en charge par Microsoft.

## <a name="what-is-acs"></a>Quelles sont les ACS ?

La plupart des développeurs ne sont pas des experts identité et ne souhaitez généralement pas intégralement mécanismes d’authentification et d’autorisation développement heure pour leurs applications et services. ACS est un service Azure qui offre un moyen facile d’authentification des utilisateurs qui ont besoin d’accéder à vos applications web et services sans avoir à logique d’authentification complexes dans votre code.

Les fonctionnalités suivantes sont disponibles dans ACS :

-   Intégration avec Windows Identity Foundation (WIF).
-   Prise en charge des fournisseurs d’identité web les plus courants (adresses IP), y compris Windows Live ID, Google, Yahoo! et Facebook.
-   Prise en charge des Services de fédération Active Directory (AD FS) 2.0.
-   Un Open Data Protocol (OData)-en fonction du service de gestion des qui fournit l’accès par programme aux paramètres ACS.
-   Portail de gestion qui permet l’accès administratif aux paramètres ACS.

Pour plus d’informations sur ACS, consultez [Access contrôle Service 2.0][].

## <a name="concepts"></a>Concepts

Azure ACS est basé sur les identités d’identité basée sur les revendications - une approche cohérente pour la création de mécanismes d’authentification pour les applications en cours d’exécution en local ou dans le cloud. Identité basée sur les revendications fournit une pratique courante pour les applications et services obtenir les informations d’identité que nécessaires sur les utilisateurs au sein de leur organisation, d’autres organisations et sur Internet.

Pour effectuer les tâches dans ce guide, vous devez comprendre les concepts suivants :

**Client** - dans le cadre de cette procédure, c’est un navigateur qui essaie d’accéder à votre application web.

**Application de fête (RP) Relying** - application RP un est un site Web ou un service qui externalise authentification à une autorité externe. Dans jargon d’identité, nous disons que le RP approuve cette autorité. Ce guide explique comment configurer votre application de gestion de la confidentialité ACS.

**Jetons** - un jeton est un ensemble de données de sécurité sont généralement émises lors de l’authentification d’un utilisateur. Il contient un ensemble de *revendications*, attributs de l’utilisateur authentifié. Une demande de remboursement peut représenter un nom d’utilisateur, un identificateur pour un rôle d’un utilisateur appartient, l’âge d’un utilisateur et ainsi de suite. Un jeton généralement signé numériquement, ce qui signifie qu’il peut provenir toujours revenir à son émetteur et son contenu ne peut pas être falsifié. Un utilisateur peut accéder à une application RP en présentant un jeton valide émis par une autorité qui approuve l’application RP.

**Fournisseur d’identité (IP)** - IP est une autorité qui authentifie l’identité des utilisateurs et émet des jetons de sécurité. Le travail réel de délivrer des jetons est activée si un service spécial appelé Service jeton de sécurité (STS). Exemples d’adresses IP : identifiant Windows Live ID, Facebook, entreprise utilisateur référentiels (par exemple, Active Directory) et ainsi de suite.
Lorsque ACS est configuré pour faire confiance à une adresse IP, le système accepte et valide des jetons émis par cette IP. ACS fiables plusieurs adresses IP à la fois, ce qui signifie que lorsque votre application approuve ACS, vous pouvez instantanément offrir votre application pour tous les utilisateurs authentifiés à partir de toutes les adresses IP que ACS approuve en votre nom.

**Fournisseur de fédération (PF)** - adresses IP connaissent le d’utilisateurs et s’authentifier à l’aide de leurs informations d’identification et émettre revendications sur ce qu’ils savent les concernant. Un fournisseur de fédération (PF) est un autre type d’autorité : plutôt que d’authentification des utilisateurs directement, il sert une authentification intermédiaire et intermédiaires financiers entre un RP et une ou plusieurs adresses IP. Adresses IP et i émettent des jetons de sécurité, par conséquent, ils utilisent tous deux Services jeton de sécurité (STS). ACS est un PF.

**Moteur de règles ACS** - la logique permet de transformer des jetons entrants à partir des adresses IP autorisées à jetons sont destinées à être consommées par le RP est codifiée sous forme de règles de transformation de revendications simple. ACS comporte un moteur de règle qui prend en charge de l’application de la logique de transformation que vous avez spécifié pour votre RP.

**ACS Namespace** - un espace de noms est une partition de niveau supérieur de ACS que vous utilisez pour organiser vos paramètres. Un espace de noms conserve une liste d’adresses IP que vous faites confiance, les applications RP souhaitées pour traiter, moteur pour traiter les jetons entrants avec les règles que vous attendez la règle et ainsi de suite. Un espace de noms expose différents points de terminaison servira par l’application et le développeur à obtenir ACS pour exécuter cette fonction.

La figure suivante illustre le fonctionne de l’authentification ACS avec une application web :

![Diagramme de flux ACS][acs_flow]

1.  Le client (dans ce cas un navigateur) requiert une page depuis le RP.
2.  Étant donné que la requête n’est pas encore authentifié, le RP redirige l’utilisateur à l’autorité qu’il approuve, c'est-à-dire ACS. ACS présente à l’utilisateur avec le choix des adresses IP qui a été spécifié pour cette RP. L’utilisateur sélectionne l’adresse IP approprié.
3.  Le client accède à la page d’authentification de l’adresse IP et vous invite à l’utilisateur pour vous connecter.
4.  Une fois que le client est authentifié (par exemple, l’identité de l’entrée des informations d’identification), l’adresse IP émet un jeton de sécurité.
5.  Après avoir exécuté un jeton de sécurité, l’adresse IP redirige le client vers ACS et le client envoie le jeton de sécurité émis par l’adresse IP à ACS.
6.  ACS valide le jeton de sécurité émis par la période d’enquête, entrées l’identité revendications dans ce jeton dans le moteur de règles ACS, calcule les revendications d’identité de sortie et émet un nouveau jeton de sécurité contenant ces déclarations de sortie.
7.  ACS redirige le client vers le RP. Le client envoie le nouveau jeton de sécurité émis par ACS au RP. Le RP valide la signature du jeton de sécurité émis par ACS, valide les revendications dans ce jeton et renvoie la page qui a été demandée.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les tâches dans ce guide, vous devez les éléments suivants :

- Un développeur Kit JDK (Java), v 1,6 ou version ultérieure.
- Eclipse IDE pour les développeurs EE Java, Indigo ou version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>. 
- Distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat, GlassFish, serveur d’applications JBoss ou jetée.
- un abonnement Azure peut être posé à partir de <http://www.microsoft.com/windowsazure/offers/>.
- La boîte à outils Azure pour Eclipse, avril 2014 relâchez ou version ultérieure. Pour plus d’informations, voir [la boîte à outils Azure pour Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Un certificat X.509 à utiliser avec votre application. Ce certificat dans certificat public (.cer) et échange d’informations personnelles est nécessaire (. Format PFX). (Options pour la création de ce certificat seront décrite plus loin dans ce didacticiel).
- Vous familiariser avec la Azure calculer émulateur et les techniques de déploiement décrits en [créant une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Créer un Namespace ACS

Pour commencer à l’aide du Service de contrôle d’accès (ACS) dans Azure, vous devez créer un espace de noms ACS. L’espace de noms fournit une portée unique pour l’adressage ressources ACS à partir de votre application.

1. Connectez-vous au [portail de gestion Azure][].
2. Cliquez sur **Active Directory**. 
3. Pour créer un nouvel espace de noms de contrôle d’accès, cliquez sur **Nouveau**et cliquez sur **Services d’application**, cliquez sur **Contrôle d’accès**, puis cliquez sur **Création rapide**. 
4. Entrez un nom pour l’espace de noms. Azure vérifie que le nom est unique.
5. Sélectionnez la région dans lequel l’espace de noms est utilisé. Pour optimiser les performances, utilisez la zone dans laquelle vous déployez votre application.
6. Si vous avez plusieurs abonnements, sélectionnez l’abonnement auquel vous souhaitez utiliser pour l’espace de noms ACS.
7. Cliquez sur **créer**.

Azure crée et Active l’espace de noms. Attendez que l’état de l’espace de noms est **actif** avant de poursuivre. 

## <a name="add-identity-providers"></a>Ajouter des fournisseurs d’identité

Dans cette tâche, vous ajoutez des adresses IP à utiliser avec votre application RP pour l’authentification. À des fins de démonstration, cette tâche montre comment ajouter Windows Live comme une adresse IP, mais vous pouvez aussi utiliser une des adresses IP répertoriée dans le portail de gestion ACS.


1.  Dans le [Portail de gestion Azure][], cliquez sur **Active Directory**et sélectionnez un espace de noms de contrôle d’accès, puis cliquez sur **Gérer**. Le portail de gestion ACS s’ouvre.
2.  Dans le volet de navigation gauche du portail de gestion ACS, cliquez sur **fournisseurs d’identité**.
3.  Windows Live ID est activée par défaut et ne peuvent pas être supprimée. Dans le cadre de ce didacticiel, uniquement Windows Live ID est utilisé. Cet écran, cependant, est l’endroit où vous pouvez ajouter d’autres adresses IP, en cliquant sur le bouton **Ajouter** .

Identifiant Windows Live ID est maintenant défini comme une adresse IP pour votre espace de noms ACS. Ensuite, vous spécifiez votre application web Java (pour être créés plus tard) comme un RP.

## <a name="add-a-relying-party-application"></a>Ajouter une application de fête confiance

Dans cette tâche, vous configurez ACS pour reconnaître votre application web Java comme une application RP valide.

1.  Dans le portail de gestion ACS, cliquez sur **applications tierces Relying**.
2.  Dans la page de **Compter des Applications tierces** , cliquez sur **Ajouter**.
3.  Dans la page **Ajouter une Application lieu d’utiliser des tiers** , procédez comme suit :
    1.  Dans la zone **nom**, tapez le nom du RP. Dans le cadre de ce didacticiel, tapez **Azure Web App**.
    2.  En **Mode**, sélectionnez **entrer les paramètres manuellement**.
    3.  Dans le **domaine**, tapez l’URI à laquelle s’applique le jeton de sécurité émis par ACS. Pour cette tâche, tapez **http://localhost : 8080 /**.
        ![Domaine de fête confiance à utiliser dans émulateur de calcul][relying_party_realm_emulator]
    4.  **Renvoyer** les URL, tapez l’URL dans laquelle ACS retourne le jeton de sécurité. Pour cette tâche, tapez **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying fête renvoyer URL à utiliser dans émulateur de calcul][relying_party_return_url_emulator]
    5.  Acceptez les valeurs par défaut dans le reste des champs.

4.  Cliquez sur **Enregistrer**.

Vous avez correctement configuré votre application web Java lorsqu’il est exécuté dans l’émulateur cluster Azure (en http://localhost : 8080 /) pour être un RP dans votre espace de noms ACS. Ensuite, créez les règles appliquées par ACS utilise pour traiter les réclamations au titre de RP.

## <a name="create-rules"></a>Créer des règles

Dans cette tâche, vous définissez les règles appliquées par le lecteur comment revendications transmises d’adresses IP à votre RP. Dans ce guide, nous allons simplement configurer ACS pour copier les types de déclaration d’entrée et les valeurs directement dans le jeton de sortie, sans filtrage ni les modifier.

1.  Dans la page principale du portail de gestion ACS, cliquez sur **groupes de règles**.
2.  Dans la page **Groupes de règles** , cliquez sur **Groupe de règles par défaut Azure Web App**.
3.  Dans la page **Modifier le groupe de règle** , cliquez sur **Générer**.
4.  Sur la **Générer des règles : par défaut règle groupe Azure Web App** page, assurez-vous que Windows Live ID est cochée, puis sur **Générer**.    
5.  Dans la page **Modifier le groupe de règle** , cliquez sur **Enregistrer**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Télécharger un certificat dans votre espace de noms ACS

Dans cette tâche, vous téléchargez un. Certificat PFX qui sera utilisé pour signer des demandes de jetons créées par votre espace de noms ACS.

1.  Dans la page principale du portail de gestion ACS, cliquez sur **certificats et des clés**.
2.  Dans la page **certificats et les clés** , cliquez sur **Ajouter** au-dessus de la **Signature des jetons**.
3.  Dans la page **certificat de signature de jetons ajouter ou clé** :
    1. Dans la section **utilisé (s) pour** , cliquez sur **Lieu d’utiliser des applications** et sélectionnez **Azure Web App** (que vous avez défini précédemment comme le nom de votre application de fête confiance).
    2. Dans la section **Type** , sélectionnez le **Certificat X.509**.
    3. Dans la section **certificat** , cliquez sur le bouton Parcourir et recherchez le fichier de certificat X.509 que vous voulez utiliser. Il s’agit d’un. Fichier PFX. Sélectionnez le fichier et cliquez sur **Ouvrir**, puis entrez le mot de passe de certificat dans la zone de texte **mot de passe** . Notez que des fins de test, vous pouvez utiliser un automatique-signed-certificat. Pour créer un certificat auto-signé, utilisez le bouton **Nouveau** dans la boîte de dialogue **Bibliothèque de filtres ACS** (décrite plus loin), ou l’utilitaire **encutil.exe** à partir du [site Web de projet][] du Azure Starter Kit pour Java.
    4. Assurez-vous que **Rendre principal** est activée. La page **certificat de signature de jetons ajouter ou clé** doit ressembler à ce qui suit.
        ![Ajouter le certificat de signature de jetons][add_token_signing_cert]
    5. Cliquez sur **Enregistrer** pour enregistrer vos paramètres et fermer la page **certificat de signature de jetons ajouter ou clé** .

Ensuite, passez en revue les informations contenues dans la page de l’intégration des applications et copiez l’URI dont vous aurez besoin pour configurer votre application web Java à utiliser ACS.

## <a name="review-the-application-integration-page"></a>Examinez la page de l’intégration des applications

Vous trouverez toutes les informations et le code nécessaires pour configurer votre application web de Java (application RP) pour l’utiliser avec ACS dans la page d’intégration d’Application du portail de gestion ACS. Vous avez besoin de ces informations lorsque vous configurez votre application web Java pour l’authentification fédérée.

1.  Dans le portail de gestion ACS, cliquez sur **intégration de l’Application**.  
2.  Dans la page de **l’Intégration de l’Application** , cliquez sur **Pages de connexion**.
3.  Dans la page **Login intégration de la Page** , cliquez sur **Azure Web App**.

Dans la **intégration de la Page Login : Azure Web App** URL de la page, la répertoriés dans **Option 1 : lien vers une page de connexion hébergé ACS** sera utilisé dans votre application web Java. Vous avez besoin de cette valeur lorsque vous ajoutez la bibliothèque de filtre de Services Azure accès contrôle à votre application Java.

## <a name="create-a-java-web-application"></a>Créer une application web Java
1. Dans Eclipse, au niveau du menu, cliquez sur **fichier**, cliquez sur **Nouveau**, puis sur **Project Web dynamique**. (Si vous ne voyez pas **Project Web dynamique** répertoriées sous forme d’un projet disponibles après avoir cliqué sur **fichier**, **Nouveau**, puis procédez comme suit : cliquez sur **fichier**, cliquez sur **Nouveau**, cliquez sur **projet**, développer **Web**, cliquez sur **Projet Web dynamique**et cliquez sur **suivant**.) Dans le cadre de ce didacticiel, nommez le projet **MyACSHelloWorld**. (Vous assurer que vous utilisez ce nom, les étapes suivantes dans ce didacticiel vous attendre à votre fichier WAR d’être nommé MyACSHelloWorld). Votre écran sera semblable à ce qui suit :

    ![Créer un projet de Bonjour pour exampple ACS][create_acs_hello_world]

    Cliquez sur **Terminer**.
2. Dans mode d’Explorateur de projets de Eclipse, développez **MyACSHelloWorld**. Avec le bouton droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.
3. Dans la boîte de dialogue **Nouveau fichier JSP** , nommez fichier **index.jsp**. Conserver le dossier parent que MyACSHelloWorld/contenu Web, comme illustré ci-dessous :

    ![Ajouter un fichier JSP par exemple ACS][add_jsp_file_acs]

    Cliquez sur **suivant**.

4. Dans la boîte de dialogue **Sélectionner un modèle de JSP** , sélectionnez **Nouveau fichier JSP (html)** , puis cliquez sur **Terminer**.
5. Lorsque le fichier index.jsp s’ouvre dans Eclipse, ajoutez dans le texte à afficher **ACS Bonjour !** dans l’objet existant `<body>` élément. Votre mise à jour `<body>` contenu doit apparaître comme suit :

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Enregistrer index.jsp.
  
## <a name="add-the-acs-filter-library-to-your-application"></a>Ajouter la bibliothèque de filtre ACS à votre application

1. Dans l’Explorateur de projets de Eclipse, avec le bouton droit **MyACSHelloWorld**, cliquez sur **Créer un chemin d’accès**, puis cliquez sur **Configurer un chemin de créer**.
2. Dans la boîte de dialogue **Chemin d’accès de Java créer** , cliquez sur l’onglet **bibliothèques** .
3. Cliquez sur **Ajouter une bibliothèque**.
4. **Filtre de Services Azure Access contrôle (en MS Tech ouvrir)** , puis cliquez sur **suivant**. La boîte de dialogue **Filtre de Services contrôle Azure Access** s’affiche.  (Le champ **emplacement** peut avoir un chemin d’accès différente, selon l’endroit où vous avez installé Eclipse, et le numéro de version peut être différent, en fonction des mises à jour logicielles).

    ![Ajouter une bibliothèque de filtre ACS][add_acs_filter_lib]

5. À l’aide d’un navigateur ouvert à la page **Login intégration de la Page** du portail de gestion, copiez l’URL répertoriée dans le **Option 1 : lien vers une page de connexion hébergé ACS** de champ et la coller dans le champ de **Point de terminaison ACS d’authentification** de la boîte de dialogue Eclipse.
6. Utiliser un navigateur ouvert à la page **Modifier une Application lieu d’utiliser des parties** du portail de gestion, copiez l’URL figurant dans le champ **domaine** et collez-le dans le champ **Domaine de fête compter** de la boîte de dialogue Eclipse.
7. Dans la section **sécurité** de la boîte de dialogue Eclipse, si vous voulez utiliser un certificat existant, cliquez sur **Parcourir**, accédez au certificat que vous souhaitez utiliser, sélectionnez-la et cliquez sur **Ouvrir**. Ou, si vous voulez créer un nouveau certificat, cliquez sur **Nouveau** pour afficher la boîte de dialogue **Nouveau certificat** , puis spécifiez le mot de passe, le nom du fichier .cer et le nom du fichier .pfx pour le nouveau certificat.
8. Vérifier **le certificat dans le fichier WAR incorporer**. Incorporation le certificat de cette manière l’inclut dans votre déploiement sans avoir à ajouter manuellement en tant que composant. (Si vous devez à la place stocker votre certificat avec l’extérieur de votre fichier WAR, vous ajouter le certificat comme un composant de rôle, décochez la case **incorporer le certificat dans le fichier WAR**.)
9. [Facultatif] Conserver les **connexions HTTPS nécessitent** activée. Si vous définissez cette option, vous devez accéder à votre application en utilisant le protocole HTTPS. Si vous ne voulez pas requièrent des connexions HTTPS, désactivez cette option.
10. Pour un déploiement vers l’émulateur cluster, vos paramètres de **Filtre de ACS Azure** seront semblable à ce qui suit.

    ![Paramètres de filtre ACS Azure pour un déploiement vers l’émulateur cluster][add_acs_filter_lib_emulator]

11. Cliquez sur **Terminer**.
12. Cliquez sur **Oui** lorsqu’il reçoit avec une boîte de dialogue indiquant qu’un fichier web.xml sera créé.
13. Cliquez sur **OK** pour fermer la boîte de dialogue **Chemin d’accès de Java créer** .

## <a name="deploy-to-the-compute-emulator"></a>Déployer vers l’émulateur cluster

1. Dans l’Explorateur de projets de Eclipse, avec le bouton droit **MyACSHelloWorld**, cliquez sur **Azure**, puis cliquez sur **Package pour Azure**.
2. **Nom du projet**, tapez **MyAzureACSProject** et cliquez sur **suivant**.
3. Sélectionnez un JDK et serveur d’applications. (Ces étapes sont décrites en détail dans le didacticiel [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).
4. Cliquez sur **Terminer**.
5. Cliquez sur le bouton **exécuter dans Azure émulateur** .
6. Une fois que votre application web Java démarre dans l’émulateur cluster, fermez toutes les instances de votre navigateur (de sorte que les sessions de navigateur en cours ne perturbent pas votre test de connexion ACS).
7. Exécuter votre application en ouvrant <http://localhost :> 8080/MyACSHelloWorld/dans votre navigateur ( <> ou/https://localhost:8080/MyACSHelloWorld/si vous avez activé **les connexions Require HTTPS**). Vous devez fournir un identifiant Windows Live ID, puis vous devez prendre pour l’URL de renvoi spécifiée pour votre application de fête confiance.
99.  Lorsque vous avez terminé votre application d’affichage, cliquez sur le bouton **Réinitialiser Azure émulateur** .

## <a name="deploy-to-azure"></a>Déploiement d’Azure

Pour déployer sur Azure, vous devez modifier le domaine de fête confiance et renvoyer URL de votre espace de noms ACS.

1. Dans le portail de gestion Azure, dans la page **Modifier une Application lieu d’utiliser des tiers** , de modifier **domaine** pour l’URL de votre site déployé. Remplacez **exemple** avec le nom DNS que vous avez spécifié pour votre déploiement.

    ![Domaine de fête confiance pour une utilisation en production][relying_party_realm_production]

2. Modifier l' **URL de renvoi** pour donner l’URL de votre application. Remplacez **exemple** avec le nom DNS que vous avez spécifié pour votre déploiement.

    ![Lieu d’utiliser des URL de renvoi tiers à utiliser dans production][relying_party_return_url_production]

3. Cliquez sur **Enregistrer** pour enregistrer votre domaine fête réponse mis à jour et retourner des modifications de l’URL.
4. Conserver la page de **Connexion Page intégration** ouverte dans votre navigateur, vous devez vous copiez peu de temps.
5. Dans l’Explorateur de projets de Eclipse, avec le bouton droit **MyACSHelloWorld**, cliquez sur **Créer un chemin d’accès**, puis cliquez sur **Configurer un chemin de créer**.
6. Cliquez sur l’onglet **bibliothèques** et cliquez sur **Filtre de Services Azure accès contrôle**, puis cliquez sur **Modifier**.
7. À l’aide d’un navigateur ouvert à la page **Login intégration de la Page** du portail de gestion, copiez l’URL répertoriée dans le **Option 1 : lien vers une page de connexion hébergé ACS** de champ et la coller dans le champ de **Point de terminaison ACS d’authentification** de la boîte de dialogue Eclipse.
8. Utiliser un navigateur ouvert à la page **Modifier une Application lieu d’utiliser des parties** du portail de gestion, copiez l’URL figurant dans le champ **domaine** et collez-le dans le champ **Domaine de fête compter** de la boîte de dialogue Eclipse.
9. Dans la section **sécurité** de la boîte de dialogue Eclipse, si vous voulez utiliser un certificat existant, cliquez sur **Parcourir**, accédez au certificat que vous souhaitez utiliser, sélectionnez-la et cliquez sur **Ouvrir**. Ou, si vous voulez créer un nouveau certificat, cliquez sur **Nouveau** pour afficher la boîte de dialogue **Nouveau certificat** , puis spécifiez le mot de passe, le nom du fichier .cer et le nom du fichier .pfx pour le nouveau certificat.
10. Conserver **le certificat dans le fichier WAR incorporer** activée, en supposant que vous souhaitez incorporer le certificat dans le fichier WAR.
11. [Facultatif] Conserver les **connexions HTTPS nécessitent** activée. Si vous définissez cette option, vous devez accéder à votre application en utilisant le protocole HTTPS. Si vous ne voulez pas requièrent des connexions HTTPS, désactivez cette option.
12. Pour un déploiement Azure, vos paramètres de filtre de ACS Azure seront semblable à ce qui suit.

    ![Paramètres de filtre ACS Azure pour un déploiement de production][add_acs_filter_lib_production]

13. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Modifier la bibliothèque** .
14. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de MyACSHelloWorld** .
15. Dans Eclipse, cliquez sur le bouton **publier dans Azure Cloud** . Répondez aux invites, similaires comme traité dans la section **à déployer votre application dans Azure** de la rubrique [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Une fois que votre application web a été déployée, fermez toutes les sessions d’ouvrir un navigateur, exécuter votre application web, et vous devez être invité à vous connecter avec les informations d’identification Windows Live ID, suivies envoyé à l’URL de renvoi de votre application de fête confiance.

Lorsque vous avez terminé à l’aide de votre application Hello World ACS, n’oubliez pas de supprimer le déploiement (vous découvrez comment supprimer un déploiement dans la rubrique [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).


## <a name="next_steps"></a>Étapes suivantes

Pour un examen de la sécurité SAML Assertion Markup Language () renvoyée par ACS à votre application, voir [comment afficher SAML renvoyée par le Service de contrôle d’accès Azure][]. Pour approfondir Explorez fonctionnalité de ACS et pour tester les scénarios plus sophistiqués, voyez [Access contrôle Service 2.0][].

En outre, cet exemple utilisé l’option **incorporer le certificat dans le fichier WAR** . Cette option facilite le déployez le certificat. Si vous voulez à la place séparer votre certificat de signature de votre fichier WAR, vous pouvez utiliser la technique suivante :

1. Dans la section **sécurité** de la boîte de dialogue **Filtre de Services Azure accès contrôle** , tapez $ **{environnement. JAVA_HOME}/myCert.cer** et décochez la case **incorporer le certificat dans le fichier WAR**. (Ajuster mycert.cer si votre nom de fichier de certificat est différent). Cliquez sur **Terminer** pour fermer la boîte de dialogue.
2. Copiez le certificat en tant que composant dans votre déploiement : Explorateur de projets dans Éclipse, développez **MyAzureACSProject**, droit **WorkerRole1**, cliquez sur **Propriétés**, développez **Rôle Azure**, puis cliquez **composants**.
3. Cliquez sur **Ajouter**.
4. Dans la boîte de dialogue **Ajouter un composant** :
    1. Dans la section **Importer** :
        1. Utilisez le bouton **fichier** pour atteindre le certificat que vous voulez utiliser. 
        2. **Méthode**, sélectionnez **Copier**.
    2. Pour **Nom en tant que**, cliquez sur la zone de texte, acceptez le nom par défaut.
    3. Dans la section **déployer** :
        1. **Méthode**, sélectionnez **Copier**.
        2. Pour **au répertoire**, tapez **% JAVA_HOME**.
    4. Votre boîte de dialogue **Ajouter un composant** doit ressembler à ce qui suit.

        ![Ajouter le composant de certificat][add_cert_component]

    5. Cliquez sur **OK**.

À ce stade, votre certificat doivent figurer dans votre déploiement. Notez que que vous incorporez le certificat dans le fichier WAR ou ajoutez en tant que composant à votre déploiement, vous avez besoin de télécharger le certificat à votre espace de noms comme décrit dans la section [télécharger un certificat à votre espace de noms ACS][] .

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Télécharger un certificat dans votre espace de noms ACS]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[site Web de projet]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Comment afficher SAML renvoyée par le Service de contrôle d’accès Azure]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Service de contrôle d’accès 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portail de gestion Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 
