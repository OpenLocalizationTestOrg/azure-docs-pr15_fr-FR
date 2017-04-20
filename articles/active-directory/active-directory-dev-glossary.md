<properties
   pageTitle="Glossaire du développeur Azure Active Directory | Microsoft Azure"
   description="Une liste de termes pour les plus fréquemment utilisés concepts du développeur Azure Active Directory et fonctionnalités."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Glossaire de développeur Azure Active Directory
Cet article contient des définitions de certains les concepts fondamentaux Azure Active Directory (AD) pour les développeurs, qui sont utiles pour en savoir plus sur développement d’applications pour Azure AD.

## <a name="access-token"></a>jeton d’accès
Un type de [jeton de sécurité](#security-token) émis par un [serveur d’autorisation](#authorization-server)et utilisé par une [application cliente](#client-application) afin d’accéder à un [protégé ressources serveur](#resource-server). En règle générale sous la forme d’un [Jeton JSON Web (JWT)][JWT], le jeton incorpore l’autorisation accordée au client par le [propriétaire de la ressource](#resource-owner), pour un niveau d’accès demandé. Le jeton contient toutes les [revendications](#claim) applicables sur le sujet, l’activation de l’application cliente à utiliser comme formulaire d’information d’identification lorsque vous accédez à une ressource donnée. Cela évite également le propriétaire de la ressource exposer les informations d’identification pour le client.

Les jetons d’accès sont parfois appelés « Utilisateur + application » ou « Application seule », selon les informations d’identification qui est représentées. Par exemple, lorsqu’une application cliente utilise le :

- [Accorder l’autorisation « Code d’autorisation »](#authorization-grant), l’utilisateur final authentifie tout d’abord en tant que propriétaire de la ressource, délégation de l’autorisation au client pour accéder à la ressource. Le client s’authentifie par la suite lors de l’obtention du jeton d’accès. Le jeton peut être parfois plus précisément en tant que « Utilisateur + application » jeton, car elle représente à la fois l’utilisateur qui a autorisé l’application cliente et l’application.
- [Accorder l’autorisation « Informations d’identification client »](#authorization-grant), le client fournit l’authentification unique, fonctionne sans autorisation/de-propriétaire de la ressource authentification, afin que le jeton peut parfois être appelé un jeton « Application seule ».

Voir [informations de référence Azure AD jeton] [ AAD-Tokens-Claims] pour plus d’informations.

## <a name="application-manifest"></a>manifeste d’application  
Une fonctionnalité fournie par le [portail classique Azure][AZURE-classic-portal], qui produit une représentation JSON de configuration d’identité de l’application, utilisée comme mécanisme de pour mettre à jour son [Application] associée[ AAD-Graph-App-Entity] et [ServicePrincipal] [ AAD-Graph-Sp-Entity] entités. Voir la rubrique [présentation le manifeste d’application Azure Active Directory] [ AAD-App-Manifest] pour plus d’informations.

## <a name="application-object"></a>objet application  
Lorsque vous register/mise à jour une application dans le [portail classique Azure][AZURE-classic-portal], le portail crée/mises à jour un objet application et un [objet principal service](#service-principal-object) correspondant pour ce client. L’objet d’application *définit* l’application de configuration d’identité globalement (pour toutes les installations où il a accès), fournissant un modèle à partir de laquelle ses objets principal service correspondant sont *dérivées* pour une utilisation localement au moment de l’exécution (dans un client spécifique).

Voir [Application et objets Principal du Service] [ AAD-App-SP-Objects] pour plus d’informations.

## <a name="application-registration"></a>inscription de l’application  
Pour permettre à une application pour intégrer et déléguer des fonctions de gestion des identités et l’accès à Azure Active Directory, il doit être enregistré par un Azure AD [client](#tenant). Lorsque vous enregistrez votre application avec Azure AD, vous fournissez une configuration identité pour votre application, ce qui permet d’intégrer Azure AD et utiliser des fonctionnalités telles que :

- Gestion fiable de l’authentification unique à l’aide de la gestion des identités Azure AD et [Connecter des OpenID] [ OpenIDConnect] implémentation de protocole
- Traité accès à [des ressources protégées](#resource-server) par [les applications clientes](#client-application), via la mise en œuvre d’Azure AD OAuth 2.0 [server d’autorisation](#authorization-server)
- [Consentement framework](#consent) pour gérer l’accès client aux ressources protégées, en fonction de l’autorisation de propriétaire de la ressource.

Consultez [applications intégration avec Azure Active Directory] [ AAD-Integrating-Apps] pour plus d’informations.

## <a name="authentication"></a>authentification
Action d’attaquer une partie des informations d’identification légitimes, qui fournit des base pour la création d’une entité de sécurité à utiliser pour le contrôle d’accès et d’identité. Par exemple, pendant un [accorder l’autorisation oauth2 ne](#authorization-grant) la partie de l’authentification remplit le rôle de [propriétaire de la ressource](#resource-owner) ou [application cliente](#client-application), selon l’octroi utilisé.

## <a name="authorization"></a>autorisation
Le fait d’accorder une autorisation de principal de sécurité authentifié effectuer une opération. Il existe deux principaux exemples d’utilisation dans le modèle de programmation Azure AD :

- Au cours d’un flux [d’accorder l’autorisation oauth2 ne](#authorization-grant) : lorsque le [propriétaire de la ressource](#resource-owner) accorde l’autorisation de l' [application cliente](#client-application), permettant au client d’accéder aux ressources du propriétaire de la ressource.
- Lors de l’accès aux ressources par le client : tel qu’implémenté par le [serveur de ressources](#resource-server), à l’aide de la [réclamer](#claim) présentes sur le [jeton d’accès](#access-token) pour prendre des décisions de contrôle d’accès basées sur les.

## <a name="authorization-code"></a>code d’autorisation
Une courte durée de vie « jeton » fournis à une [application cliente](#client-application) par [point de terminaison d’autorisation](#authorization-endpoint), dans le cadre du flux « code d’autorisation », d’une des d’oauth2 ne quatre [accorde l’autorisation](#authorization-grant). Le code est renvoyé à l’application cliente en réponse à l’authentification d’un [propriétaire de la ressource](#resource-owner), indiquant que le propriétaire de la ressource a délégué d’autorisation pour accéder aux ressources demandées. Dans le cadre du flux, le code est perdu plus loin pour un [jeton d’accès](#access-token).

## <a name="authorization-endpoint"></a>point de terminaison d’autorisation
Un points de terminaison implémentées par le [serveur d’autorisation](#authorization-server), utilisées pour interagir avec le [propriétaire de la ressource](#resource-owner) afin de fournir un [accorder l’autorisation](#authorization-grant) lors d’une autorisation oauth2 n’accorder flux. Selon le flux d’accorder l’autorisation utilisé, le don effectivement fourni peut varier, incluant un [code d’autorisation](#authorization-code) ou [jeton de sécurité](#security-token).

Consultez la spécification oauth2 ne [autorisation accorder types] [ OAuth2-AuthZ-Grant-Types] et [point de terminaison d’autorisation] [ OAuth2-AuthZ-Endpoint] sections, ainsi que la [spécification OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] pour plus d’informations.

## <a name="authorization-grant"></a>accorder l’autorisation
Informations d’identification qui représente l' [du propriétaire de la ressource](#resource-owner) [l’autorisation](#authorization) d’accéder à ses ressources protégés, accordées à une [application cliente](#client-application). Une application cliente peut utiliser une des [quatre types de grant définies par la structure d’autorisation oauth2 ne] [ OAuth2-AuthZ-Grant-Types] pour obtenir un don, selon le type de client/configuration requise : « code octroi », « accorder des informations d’identification client », « accorder implicite » et « accorder des informations d’identification de mot de passe de propriétaire de la ressource ». Les informations d’identification retournées au client sont un [jeton d’accès](#access-token)ou un [code d’autorisation](#authorization-code) (échangés ultérieurement pour un jeton d’accès), selon le type d’accorder l’autorisation utilisée.

## <a name="authorization-server"></a>serveur d’autorisation
Telle que définie par la [Structure d’autorisation oauth2 ne][OAuth2-Role-Def], le serveur responsable de délivrer access jetons au [client](#client-application) une fois le [propriétaire de la ressource](#resource-owner) d’authentification et obtenir son autorisation réussie. Une [application cliente](#client-application) interagit avec le serveur d’autorisation lors de l’exécution via l' [autorisation](#authorization-endpoint) et points de terminaison [jeton](#token-endpoint) , conformément à l’oauth2 ne définissent [accorde l’autorisation](#authorization-grant).

Dans le cas d’intégration d’applications Azure AD, Azure AD mettent en œuvre, le rôle de serveur d’autorisation pour les applications Azure AD et Microsoft API de service, par exemple [Microsoft Graph API][Microsoft-Graph].

## <a name="claim"></a>réclamer
Un [jeton de sécurité](#security-token) contient revendications, qui fournissent des assertions environ une entité (par exemple, une [application cliente](#client-application) ou [propriétaire de la ressource](#resource-owner)) à une autre entité (par exemple, le [serveur de ressources](#resource-server)). Revendications sont des paires nom/valeur de relais faits à propos de l’objet jeton (par exemple, l’entité de sécurité qui a été authentifiée par le [serveur d’autorisation](#authorization-server)). Les revendications présentes dans un jeton donné dépendent de plusieurs variables, notamment le type de jeton, le type d’informations d’identification utilisées pour authentifier l’objet, la configuration de l’application, etc..

Voir [référence de jeton Azure AD] [ AAD-Tokens-Claims] pour plus d’informations.

## <a name="client-application"></a>application cliente  
Telle que définie par la [Structure d’autorisation oauth2 ne][OAuth2-Role-Def], une application qui vous paraît protégé demandes de ressources pour le compte le [propriétaire de la ressource](#resource-owner). Le terme « client » n’implique pas les caractéristiques d’implémentation matériel particulier (par exemple, si l’application est exécutée sur un serveur, un ordinateur de bureau ou d’autres appareils).  

Une application cliente demande à partir d’un propriétaire de la ressource à participer à un flux [d’autorisation oauth2 ne accorder](#authorization-grant) une [autorisation](#authorization) et peut-être accéder aux API/données en nom du propriétaire de la ressource. La structure d’autorisation oauth2 ne [définit deux types de clients][OAuth2-Client-Types], « confidentiel » et « public », en fonction de la capacité du client pour préserver la confidentialité de ses informations d’identification. Applications peuvent implémenter un [WebClient (confidentiel)](#web-client) qui s’exécute sur un serveur web, un [client natif (public)](#native-client) est installé sur un appareil ou un [agent - client par utilisateur (public)](#user-agent-based-client) qui s’exécute dans le navigateur d’un périphérique.

## <a name="consent"></a>consentement
Le processus d’un [propriétaire de la ressource](#resource-owner) l’octroi d’autorisations pour une [application cliente](#client-application), des [autorisations](#permissions) spécifiques à accéder aux ressources protégées, au nom de propriétaire de la ressource. En fonction des autorisations requises par le client, un administrateur ou un utilisateur est invité à consentement pour autoriser l’accès à leurs données organisation/personne respectivement. Notez que dans un scénario de [client multiples](#multi-tenant-application) , l’application [service principal](#service-principal-object) est également enregistré dans le client de l’utilisateur terme autorisation.

## <a name="id-token"></a>Jeton d’ID
Une [Connexion OpenID] [ OpenIDConnect-ID-Token] [du jeton de sécurité](#security-token) fourni par un [point de terminaison d’autorisation](#authorization-endpoint), comportant des [revendications](#claim) relatifs à l’authentification d’un utilisateur final [propriétaire de la ressource [serveur d’autorisation](#authorization-server) ](#resource-owner). Comme un jeton d’accès jetons ID apparaissent également sous la forme d’un signé numériquement [JSON Web jeton (JWT)][JWT]. Contrairement à un jeton d’accès, revendications du jeton d’un ID ne sont pas utilisées pour des objectifs liés à l’accès aux ressources et contrôle d’accès en particulier.

Voir [référence de jeton Azure AD] [ AAD-Tokens-Claims] pour plus d’informations.

## <a name="multi-tenant-application"></a>application cliente multiples
Un cours de l' [application cliente](#client-application) qui permet de se connecter et [consentement](#consent) par les utilisateurs mis en service dans n’importe quel Azure AD [client](#tenant), y compris les clients différent de celui où le client est enregistré. En revanche, une application enregistrée comme unique-client, serait autoriser uniquement les connexions à partir de comptes d’utilisateurs mis en service dans le client même que celui où l’application est enregistrée. Applications [clientes native](#native-client) sont multi-clients par défaut, tandis que les applications [clientes web](#web-client) ont la possibilité de choisir entre unique et client multiples.

Découvrez [comment connecter tout utilisateur Azure AD en utilisant le modèle d’application multi-client] [ AAD-Multi-Tenant-Overview] pour plus d’informations.

## <a name="native-client"></a>native client
Un type d' [application cliente](#client-application) qui est installé en mode natif sur un appareil. Étant donné que tout le code est exécuté sur un appareil, il est considéré comme un client « public » en raison de son incapacité pour stocker les informations d’identification en privé/confidentielle. Voir [les profils et les types de clients oauth2 ne] [ OAuth2-Client-Types] pour plus d’informations.

## <a name="permissions"></a>autorisations
Une [application cliente](#client-application) accède à un [serveur de ressources](#resource-server) en déclarer des demandes d’autorisation. Deux types sont disponibles :

- Les autorisations « Déléguées » demandent l’accès [en fonction de l’étendue](#scopes) sous délégués d’autorisation du [propriétaire de la ressource](#resource-owner)connecté dans, sont présentées à la ressource au moment de l’exécution en tant que [« scp » revendications](#claim) dans le du client [jeton d’accès](#access-token).
- Les autorisations « Application » demandent l’accès [basé sur un rôle](#roles) sous informations d’identification/identité de l’application client, sont présentées à la ressource au moment de l’exécution comme [« rôles » revendications](#claim) dans jeton d’accès du client.

Ils également surface pendant le processus [de consentement](#consent) , ce qui donne l’administrateur ou propriétaire de la ressource la possibilité pour accorder/refuser l’accès client aux ressources dans leur client.

Demandes d’autorisation sont configurés sur les Applications » » / « « onglet Configurer dans le [portail classique Azure][AZURE-classic-portal], sous « Autorisations à d’autres applications », en sélectionnant la souhaité » déléguée autorisations » et « Application autorisations » (le dernier du rôle d’administrateur général). Car un [client public](#client-application) ne peut pas mettre à jour les informations d’identification, il peut uniquement demander autorisations déléguées, tandis que la possibilité de demande déléguée et les autorisations des applications dispose d’un [client confidentielles](#client-application) . Du client [objet application](#application-object) stocke les autorisations déclarées dans sa [propriété requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>propriétaire de la ressource
Telle que définie par la [Structure d’autorisation oauth2 ne][OAuth2-Role-Def], une entité capable d’octroyer l’accès à une ressource protégée. Lorsque le propriétaire de la ressource est une personne, il est appelé un utilisateur final. Par exemple, lorsqu’une [application cliente](#client-application) souhaite d’accéder aux boîtes aux lettres d’un utilisateur via [Microsoft Graph API][Microsoft-Graph], elle nécessite l’autorisation de propriétaire de la ressource de la boîte aux lettres.

## <a name="resource-server"></a>serveur de ressources
Telle que définie par la [Structure d’autorisation oauth2 ne][OAuth2-Role-Def], un serveur que hôtes protégé ressources, capables d’accepte et répond aux protégé demandes de ressources par [les applications clientes](#client-application) présentant un [jeton d’accès](#access-token). Également connu sous un serveur de ressources protégé, ou une ressource application.

Un serveur de ressources expose API et applique un accès à ses ressources protégés par le biais [étendues](#scopes) et les [rôles](#roles), à l’aide de la structure OAuth 2.0 d’autorisation. Les exemples sont l’API Azure AD Graph qui permet d’accéder à des données client Azure AD et des API Office 365 qui permettent d’accéder aux données telles que courrier et calendrier. Ces deux éléments sont également accessibles via l' [Interface API Microsoft Graph][Microsoft-Graph].  

Comme une application cliente, configuration d’identité de l’application de la ressource est établie via [l’inscription](#application-registration) d’un client Azure AD, spéciale qui permet l’application et l’objet principal de service. Certaines API fournis par Microsoft, tels que l’API Azure AD Graph, avoir enregistré des identités de service rendues disponibles dans toutes les installations pendant la mise en service.

## <a name="roles"></a>rôles
Comme [étendues](#scopes), rôles offrent un moyen d’un [serveur de ressources](#resource-server) accéder à ses ressources protégés en matière. Il existe deux types : un rôle « utilisateurs » mettent en œuvre, de contrôle d’accès basé sur un rôle pour les utilisateurs/groupes qui nécessitent un accès à la ressource, tandis qu’un rôle de « application » mettent en œuvre, de la même pour les [applications clientes](#client-application) qui nécessitent un accès.

Rôles sont des chaînes de ressource définie (par exemple « approbateur de la note de frais, » « En lecture seule », « Directory.ReadWrite.All »), gérés dans le [portail classique Azure] [ AZURE-classic-portal] par le biais [manifeste d’application](#application-manifest)de la ressource et stockée dans [appRoles propriété de la ressource][AAD-Graph-Sp-Entity]. Le portail classique Azure est également utilisé pour attribuer des rôles « utilisateurs » et configurer les [autorisations des applications](#permissions) du client pour accéder à un rôle de « application ».

Pour une description détaillée des rôles application exposé par l’API de Azure AD Graph, consultez [Graph API d’autorisation étendues][AAD-Graph-Perm-Scopes]. Pour obtenir un exemple d’implémentation pas à pas, voir [contrôle d’accès dans les applications cloud Azure AD basée sur les rôles][Duyshant-Role-Blog].

## <a name="scopes"></a>étendues
Tout comme les [rôles](#roles), étendues offrent un moyen d’un [serveur de ressources](#resource-server) accéder à ses ressources protégés en matière. Étendues sont utilisées pour implémenter [l’étendue] [ OAuth2-Access-Token-Scopes] contrôle d’accès, pour une [application cliente](#client-application) qui a été donné délégué l’accès à la ressource par son propriétaire.

Les étendues sont définis sur les ressources chaînes (par exemple « Mail.Read », « Directory.ReadWrite.All »), gérés dans le [portail classique Azure] [ AZURE-classic-portal] par le biais [manifeste d’application](#application-manifest)de la ressource et stockée dans [oauth2Permissions propriété de la ressource][AAD-Graph-Sp-Entity]. Le portail classique Azure permet également de configurer client application [délégué des autorisations](#permissions) pour accéder à l’étendue.

Une meilleure pratique convention, consiste à utiliser un format « resource.operation.constraint ». Pour une description détaillée des étendues exposés par l’API de Azure AD Graph, consultez [Graph API d’autorisation étendues][AAD-Graph-Perm-Scopes]. Pour étendues exposées par les services Office 365, voir [référence d’autorisations Office 365 API][O365-Perm-Ref].

## <a name="security-token"></a>jeton de sécurité
Un document signé contenant revendications, par exemple un jeton oauth2 n’ou assertion SAML 2.0. Pour un oauth2 [accorder l’autorisation](#authorization-grant), un [jeton d’accès](#access-token) (oauth2 ne) et un [Jeton d’ID](OpenID Connect) sont des types de jetons de sécurité, qui sont implémentées comme un [Jeton JSON Web (JWT)][JWT].

## <a name="service-principal-object"></a>objet principal de service
Lorsque vous register/mise à jour une application dans le [portail classique Azure][AZURE-classic-portal], le portail crée/mises à jour un [objet application](#application-object) et un objet principal service correspondant pour ce client. L’objet d’application *définit* la configuration l’application identité globalement (pour toutes les installations où l’application associée a accès), et est le modèle à partir de laquelle ses objets principal service correspondant sont *dérivées* pour une utilisation localement au moment de l’exécution (dans un client spécifique).

Voir [Application et objets Principal du Service] [ AAD-App-SP-Objects] pour plus d’informations.

## <a name="sign-in"></a>se connecter
Le processus d’une [application cliente](#client-application) lancement d’authentification de l’utilisateur final et la capture d’état associée, pour acquérir un [jeton de sécurité](#security-token) et étendue de la session d’application à cet état. L’état peut inclure des objets tels que des informations de profil utilisateur et informations tirées de revendications des jetons.

La fonction connexion d’une application est généralement utilisée pour implémenter l’unique-authentification (SSO). Il peut également être précédé par une fonction « inscription », comme le point d’entrée pour un utilisateur final accéder à l’application (selon la première connexion à). La fonction d’inscription est utilisée pour collecter et conserver l’état supplémentaire spécifique à l’utilisateur et peut-être nécessiter [consentement de l’utilisateur](#consent).

## <a name="sign-out"></a>déconnexion
Le processus d’authentification non un utilisateur final, détacher l’état d’utilisateur associé à la session [application cliente](#client-application) au cours de [connexion](#sign-in)

## <a name="tenant"></a>client
Une instance d’un répertoire Azure AD est appelée un client Azure AD. Il propose un certain nombre de fonctionnalités, notamment :

- un service de Registre pour les applications intégrées
- authentification des comptes d’utilisateurs et des applications enregistrées
- Points de terminaison REST doit prendre en charge différents protocoles notamment oauth2 n’et SAML, y compris [point de terminaison d’autorisation](#authorization-endpoint), [point de terminaison jeton](#token-endpoint) et le point de terminaison « courant » utilisé par les [applications clients multiples](#multi-tenant-application).

Un client est également associé à une annonce Azure ou d’abonnement Office 365 lors de la configuration de l’abonnement, fournissant des fonctionnalités d’identité et de gestion de l’accès de l’abonnement. Découvrez [comment obtenir un client Azure Active Directory] [ AAD-How-To-Tenant] pour plus d’informations sur les différentes méthodes que vous pouvez accéder à un client. Voir [comment Azure abonnements sont associés à Azure Active Directory] [ AAD-How-Subscriptions-Assoc] pour plus d’informations sur la relation entre les abonnements et un client Azure AD.

## <a name="token-endpoint"></a>point de terminaison de jeton
Un points de terminaison implémentées par le [serveur d’autorisation](#authorization-server) pour prendre en charge oauth2 [accorde l’autorisation](#authorization-grant). Selon l’octroi, il peut être utilisé pour acquérir un [jeton d’accès](#access-token) (et connexes jeton « Actualiser ») d’un [client](#client-application), ou [jeton d’ID](#ID-token) lorsqu’il est utilisé avec la [Connexion OpenID] [ OpenIDConnect] protocole.

## <a name="user-agent-based-client"></a>Agent-client par utilisateur
Un type d' [application cliente](#client-application) télécharge du code à partir d’un serveur web qui s’exécute d’un agent utilisateur (par exemple, un navigateur web), par exemple une seule Page Application (SPA). Étant donné que tout le code est exécuté sur un appareil, il est considéré comme un client « public » en raison de son incapacité pour stocker les informations d’identification en privé/confidentielle. Voir [les profils et les types de clients oauth2 ne] [ OAuth2-Client-Types] pour plus d’informations.

## <a name="user-principal"></a>utilisateur principal
Similaire à la façon dont un objet principal de service est utilisé pour représenter une instance de l’application, un objet principal d’utilisateur est un autre type de sécurité principal, qui représente un utilisateur. L' Azure AD Graph [entité utilisateur] [ AAD-Graph-User-Entity] définit le schéma pour un objet utilisateur, y compris les propriétés relatives à l’utilisateur telles que le nom et prénom, nom d’utilisateur principal, l’appartenance aux rôles de répertoire, etc.. Ainsi, la configuration d’identité utilisateur pour Azure AD établir un utilisateur principal au moment de l’exécution. L’utilisateur principal est utilisé pour représenter un utilisateur authentifié pour de l’authentification unique, [acceptez](#consent) la délégation d’enregistrement, ce qui décisions de contrôle d’accès, etc..

## <a name="web-client"></a>client Web
Un type d' [application cliente](#client-application) qui exécute tout le code sur un serveur web et en mesure de fonctionner comme un client « confidentiel » par le stockage en toute sécurité de ses informations d’identification sur le serveur. Voir [les profils et les types de clients oauth2 ne] [ OAuth2-Client-Types] pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
Le [Guide du développeur Azure AD] [ AAD-Dev-Guide] s’agit du portail à utiliser pour le développement Azure AD tous les autres thèmes, y compris une vue d’ensemble [d’intégration] d’application[ AAD-How-To-Integrate] et les bases de [l’authentification Azure Active Directory et scénarios d’authentification pris en charge][AAD-Auth-Scenarios].

Utilisez la section commentaires Disqus suivante pour fournir des commentaires et aidez-nous à affiner et mettre en forme notre contenu.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
