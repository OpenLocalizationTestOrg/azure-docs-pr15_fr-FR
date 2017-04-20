
<properties
   pageTitle="Scénarios d’authentification pour Azure AD | Microsoft Azure"
   description="Une vue d’ensemble des cinq scénarios plus courants d’authentification pour Azure Active Directory (DAS)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Scénarios d’authentification pour Azure AD

Azure Active Directory (AD Azure) simplifie l’authentification pour les développeurs en fournissant identité comme source de service, avec prise en charge des protocoles standard tels que OAuth 2.0 et OpenID se connecter, ainsi que les ouvre bibliothèques de plates-formes différentes pour vous aider à commencer à coder rapidement. Ce document vous permettra de comprendre le prend en charge des scénarios Azure AD différentes et vous montrent comment procéder. Il est divisé en sections suivantes :

- [Concepts de base de l’authentification dans Azure AD](#basics-of-authentication-in-azure-ad)

- [Revendications dans des jetons de sécurité Azure AD](#claims-in-azure-ad-security-tokens)

- [Notions de base de l’inscription d’une Application dans Azure Active Directory](#basics-of-registering-an-application-in-azure-ad)

- [Types d’applications et de scénarios](#application-types-and-scenarios)

  - [Navigateur Web pour une Application Web](#web-browser-to-web-application)

  - [Application de Page unique (SPA)](#single-page-application-spa)

  - [Application native pour API Web](#native-application-to-web-api)

  - [Application Web aux API Web](#web-application-to-web-api)

  - [Processus ou Application serveur d’API Web](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Concepts de base de l’authentification dans Azure AD

Si vous connaissez mal les concepts de base de l’authentification dans Azure AD, lisez cette section. Dans le cas contraire, vous souhaiterez peut-être ignorer jusqu'à [Types d’applications et de scénarios](#application-types-and-scenarios).

Examinons le plus simple scénario où l’identité est requise : un utilisateur dans un navigateur web doit s’authentifier à une application web. Ce scénario est décrit plus en détail dans la section de [Navigateur Web pour une Application Web](#web-browser-to-web-application) , mais il est un bon point de départ pour illustrer les capacités d’Azure AD et conceptualiser comment fonctionne le scénario. Examinons le diagramme suivant de ce scénario :

![Vue d’ensemble de l’authentification pour une application web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Avec le diagramme ci-dessus à l’esprit, voici ce que vous devez savoir sur ses divers composants :

- Azure Active Directory est le fournisseur d’identité, responsable de la vérification de l’identité des utilisateurs et des applications qui existent dans l’annuaire d’une organisation et finalement émettre des jetons de sécurité si celle-ci est réussie de ces utilisateurs et d’applications.


- Une application qui souhaite confier l’authentification Azure AD doit être enregistrée dans Azure AD, qui s’inscrit et identifie de l’application dans l’annuaire.


- Les développeurs peuvent utiliser les bibliothèques de l’authentification libres Azure AD pour faciliter l’authentification en gérant les détails du protocole pour vous. Pour plus d’informations, consultez [Azure Active Directory authentification bibliothèques](active-directory-authentication-libraries.md) .


• Une fois qu’un utilisateur a été authentifié, l’application doit valider du jeton de sécurité de l’utilisateur pour vous assurer que l’authentification a réussi pour les parties initial. Les développeurs peuvent utiliser les bibliothèques d’authentification fournis pour gérer la validation de n’importe quel jeton d’Azure Active Directory, y compris JSON Web jetons (JWT) ou SAML 2.0. Si vous souhaitez effectuer une validation manuellement, consultez la documentation [Le Gestionnaire de jetons JWT](https://msdn.microsoft.com/library/dn205065.aspx) .


> [AZURE.IMPORTANT] Azure AD utilise le chiffrement de clé publique pour signer jetons et vérifier qu’ils sont valides. Voir [Importantes d’informations sur la signature clé survol dans Azure AD](active-directory-signing-key-rollover.md) pour plus d’informations sur la logique nécessaire que vous devez disposer dans votre application pour vérifier qu’il est toujours mis à jour avec les dernières clés.


• Le flux des requêtes et les réponses pour le processus d’authentification est déterminé par le protocole d’authentification qui a été utilisé, par exemple 2.0 OAuth, OpenID à vous connecter, la fédération Web ou SAML 2.0. Ces protocoles sont présentés plus en détail dans la rubrique [Protocoles Azure Active Directory d’authentification](active-directory-authentication-protocols.md) et dans les sections ci-dessous.

> [AZURE.NOTE] Azure AD prend en charge le 2.0 OAuth et utilisent des normes OpenID connecter très souvent des jetons porteur, y compris des jetons porteur représentés sous la forme JWTs. Un jeton porteur est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce cas, « porteur » est une partie qui peut présenter le jeton. Bien que fête doit tout d’abord authentifier avec Azure AD pour recevoir le jeton porteur, si les étapes requises ne sont pas prises pour sécuriser le jeton de stockage et de transmission, il peut être interception et utilisé par un tiers involontaire. Tandis que certains jetons de sécurité ont un mécanisme intégré pour empêcher que des personnes non autorisées à l’aise, jetons PORTEUR n’ont pas ce mécanisme et doivent être acheminés dans un canal sécurisé comme TLS (HTTPS). Si un jeton porteur est transmis en clair, un homme d’attaque peut être utilisée par une partie malveillante pour obtenir le jeton et utilisez-le pour un accès non autorisé à une ressource protégée. Les mêmes principes de sécurité s’appliquent lorsque vous stockez ou la mise en cache des jetons porteur pour une utilisation ultérieure. Toujours vous assurer que votre application transmet et stocke les jetons des porteur de façon sécurisée. Pour des raisons de sécurité plus sur les jetons porteur, voir [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).


Maintenant que vous avez une vue d’ensemble des concepts de base, lisez les sections ci-dessous pour comprendre le fonctionne de mise en service dans Azure Active Directory et les scénarios courants Azure AD prend en charge.


## <a name="claims-in-azure-ad-security-tokens"></a>Revendications dans des jetons de sécurité Azure AD

Jetons de sécurité émis par Azure AD contiennent les réclamations ou assertions d’informations sur l’objet qui a été authentifié. Ces revendications peuvent être utilisées par l’application pour diverses tâches. Par exemple, ils peuvent servir pour valider le jeton, identifier client d’annuaire de l’objet, afficher les informations utilisateur, identifier l’autorisation de l’objet et ainsi de suite. Les revendications présentes dans un jeton de sécurité donnée sont VARIES selon le type de jeton, le type d’informations d’identification utilisées pour authentifier l’utilisateur et la configuration d’application. Une brève description de chaque type de réclamer émise par Azure AD est fournie dans le tableau ci-dessous. Pour plus d’informations, reportez-vous à [jeton pris en charge et les Types de revendications](active-directory-token-and-claims.md).


| Réclamer | Description |
|-------|-------------|
| ID de l’application | Identifie l’application qui utilise le jeton.
| Audience | Identifie la ressource destinataire que le jeton est destiné. |
| Application d’authentification contexte classe référence | Indique comment le client a été authentifié (client public et confidentiel client). |
| Authentification instantanée | Enregistre la date et l’heure de l’authentification. |
| Méthode d’authentification | Indique la manière dont l’objet du jeton a été authentifié (mot de passe, certificat, etc.). |
| Prénom | Fournit le prénom de l’utilisateur en tant que jeu dans Azure Active Directory. |
| Groupes | Contient des groupes d’ID de Azure AD objets de que l’utilisateur est membre du. |
| Fournisseur d’identité | Enregistre le fournisseur d’identité authentifiés l’objet du jeton. |
| Délivré à | Enregistre l’heure à laquelle le jeton a été publié, souvent utilisés pour l’actualisation jeton. |
| Émetteur | Identifie le STS qui a émis le jeton, ainsi que le client Azure AD. |
| Nom de famille | Fournit le nom de famille de l’utilisateur en tant que jeu dans Azure Active Directory. |
| Nom | Fournit une valeur lisible humaine qui identifie l’objet du jeton. |
| Id d’objet | Contient un identificateur unique et immuable de l’objet dans Azure Active Directory. |
| Rôles | Contient les noms conviviales Azure AD de rôles d’Application que l’utilisateur a été accordée. |
| Étendue | Indique les autorisations accordées à l’application cliente. |
| Objet | Indique le principal sur lequel le jeton indique les informations. |
| Id de client | Contient un identificateur unique du client directory qui a émis le jeton immuable. |
| Durée de vie des jeton | Définit l’intervalle de temps dans lequel un jeton est valide. |
| Nom d’utilisateur Principal | Contient le nom d’utilisateur principal de l’objet. |
| Version | Affiche le numéro de version du jeton. |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Notions de base de l’inscription d’une Application dans Azure Active Directory

N’importe quelle application externalise l’authentification Azure AD doit être enregistrée dans un répertoire. Cette étape consiste à parler Azure AD votre application, notamment l’URL où il est situé, l’URL pour envoyer une réponse une fois l’authentification, URI à identifier votre application et bien plus encore. Ces informations sont requises pour plusieurs raisons clés :

- Azure AD doit coordonnées pour communiquer avec l’application lors de la gestion d’authentification ou échange de jetons. Ce sont les suivants :

  - URI ID d’application : L’identificateur pour une application. Cette valeur est envoyée à Azure Active Directory lors de l’authentification pour indiquer quelle application un jeton pour permettre à l’appelant. Par ailleurs, cette valeur est incluse dans le jeton afin que l’application ne sait qu’il a été la cible prévue.


  - Répondre URL et rediriger URI : dans le cas d’une API web ou une application web, l’URL de réponse est l’emplacement auquel Azure AD vous envoie la réponse d’authentification, y compris un jeton si l’authentification a réussi. Dans le cas d’une application native, l’URI rediriger est un identificateur unique à laquelle Azure AD redirigera l’agent utilisateur dans une requête OAuth 2.0.


  - ID client : L’ID d’une application, qui est générée par Azure AD lorsque l’application est enregistrée. Lorsque vous demandez un code d’autorisation ou jeton, l’ID de client et la clé sont envoyées à Azure Active Directory lors de l’authentification.


  - Clé : Clé qui est envoyée avec un ID client lors de l’authentification à Azure AD pour appeler un API web.


- Azure AD doit s’assurer que l’application possède les autorisations requises pour accéder à vos données de l’annuaire, d’autres applications de votre organisation et ainsi de suite

Mise en service devient plus clair lorsque vous comprenez qu’il existe deux catégories d’applications qui peuvent être développées et intégrées avec Azure Active Directory :

- Demande de client unique : une application client unique est destinée à utiliser dans une organisation. Il s’agit généralement de métier applications métiers rédigées par un développeur d’entreprise. Une application client unique n’a pas besoin d’être accessible par les utilisateurs d’un annuaire, et par conséquent, elle doit uniquement être mis en service dans un répertoire. Ces applications sont généralement inscrits par un développeur de l’organisation.


- Application cliente multiples : une application cliente multiples est destinée dans de nombreuses organisations, pas simplement une organisation. Il s’agit généralement logiciel-comme-a-(SaaS) des applications de service rédigées par un éditeur de logiciels (fil). Applications clients multiples doivent être mis en service dans chaque répertoire où ils seront utilisés, ce qui nécessite une autorisation utilisateur ou un administrateur pour les enregistrer. Ce processus consentement démarre lorsqu’une application a été enregistrée dans l’annuaire et est accordée l’accès à l’API Graph ou API web peut-être un autre. Lorsqu’un utilisateur ou un administrateur à partir d’une autre organisation s’inscrit à utiliser l’application, ils sont présentés avec une boîte de dialogue qui s’affiche les autorisations que requises par l’application. L’utilisateur ou l’administrateur peut ensuite consentement à l’application, ce qui donne l’application à accéder aux données indiquées et enfin enregistre l’application dans leur répertoire. Pour plus d’informations, voir [vue d’ensemble de l’infrastructure d’autorisation](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Quelques considérations supplémentaires en cas de problème lors du développement d’une application cliente multiples au lieu d’une application client unique. Par exemple, si vous effectuez votre application disponible pour les utilisateurs dans plusieurs répertoires, vous avez besoin d’un mécanisme pour déterminer quel client ils se trouvent. Une application client unique doit uniquement rechercher dans son propre répertoire pour un utilisateur, pendant qu’une application cliente multiples a besoin identifier un utilisateur spécifique à partir de tous les répertoires dans Azure Active Directory. Pour effectuer cette tâche, Azure Active Directory offre un point de terminaison d’authentification courantes où n’importe quelle application cliente multiples peut diriger les demandes de se connecter, au lieu d’un point de terminaison client spécifiques. Ce point de terminaison est https://login.microsoftonline.com/common pour tous les répertoires dans Azure AD, tandis qu’un point de terminaison spécifiques au client peut-être être https://login.microsoftonline.com/contoso.onmicrosoft.com. Le point de terminaison courantes est particulièrement important à prendre en considération lorsque vous développez votre application, car vous devez la logique nécessaire pour gérer plusieurs clients lors de la connexion, déconnexion et jeton validation.

Si vous soyez en train de développer une application client unique mais que vous souhaitez rendre disponibles pour de nombreuses organisations, vous pouvez facilement apporter des modifications à l’application et sa configuration dans Azure AD pour le convertir en client multiples capable. En outre, Azure AD utilise la même clé de signature pour tous les jetons dans tous les répertoires, si vous fournissez l’authentification dans un seul client ou une application de cliente multiples.

Chaque scénario répertorié dans ce document comprend une section sous-adresse qui décrit les exigences de mise en service. Pour obtenir des informations plus détaillées sur l’activation d’une application dans Azure Active Directory et les différences entre les applications unique et clients à plusieurs, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md) pour plus d’informations. Poursuivre la lecture pour mieux comprendre les scénarios d’application courants dans Azure Active Directory.

## <a name="application-types-and-scenarios"></a>Types d’applications et de scénarios

Chacun des scénarios décrits dans ce document peut être développé à l’aide de différentes langues et plateformes. Ils sont tous accompagnés des exemples de code complets qui sont disponibles dans notre [guide des exemples de Code](active-directory-code-samples.md), ou directement dans les [référentiels d’exemple Github](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory)correspondante. En outre, si votre application requiert un segment d’un scénario de bout en bout ou une partie spécifique, dans la plupart des cas, ces fonctionnalités peuvent être ajoutées séparément. Par exemple, si vous avez une application native qui appelle une API web, vous pouvez facilement ajouter une application web qui appelle également l’API web. Le diagramme suivant illustre ces scénarios et les types d’applications, et comment les différents composants peuvent être ajoutés :

![Types d’applications et scénarios](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Voici les cinq scénarios application principale pris en charge par Azure AD :

- [Navigateur Web pour une Application Web](#web-browser-to-web-application): un utilisateur doit se connecter à une application web qui est sécurisée par Azure AD.

- [Application de Page unique (SPA)](#single-page-application-spa): un utilisateur doit se connecter à une application de page unique qui est sécurisée par Azure AD.

- [Application native à l’API Web](#native-application-to-web-api): une application native qui s’exécute sur un PC, une tablette ou un téléphone doit authentifier un utilisateur pour obtenir des ressources à partir d’un site web API est sécurisé par Azure AD.

- [Application Web à l’API Web](#web-application-to-web-api): une application web a besoin d’obtenir des ressources à partir d’un site web API sécurisé par Azure AD.

- [Processus ou Application serveur d’API Web](#daemon-or-server-application-to-web-api): une application de processus ou d’une application serveur sans interface utilisateur web a besoin d’obtenir des ressources à partir d’un site web API sécurisé par Azure AD.

### <a name="web-browser-to-web-application"></a>Navigateur Web pour une Application Web

Cette section décrit une application qui authentifie un utilisateur dans un navigateur web à une application web. Dans ce scénario, l’application web achemine le navigateur de l’utilisateur pour les se connecter à Azure AD. Azure AD renvoie une réponse connexion via le navigateur de l’utilisateur, qui contient des revendications relatives à l’utilisateur dans un jeton de sécurité. Ce scénario prend en charge l’authentification en utilisant les protocoles Web-Federation, SAML 2.0 et OpenID se connecter.


#### <a name="diagram"></a>Diagramme
![Flux d’authentification du navigateur pour une application web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>Description du flux de protocole


1. Lorsqu’un utilisateur visite l’application et doit se connecter, il est redirigé via une demande de connexion au point de terminaison d’authentification dans Azure Active Directory.


2. L’utilisateur se connecte sur la page de connexion.


3. Si l’authentification réussit, Azure AD crée un jeton d’authentification et renvoie une réponse se connecter à l’URL de réponse de l’application qui a été configuré dans le portail de gestion Azure. Pour une application de production, l’URL de réponse doit être HTTPS. Le jeton retourné inclut des déclarations à l’utilisateur et Azure AD qui sont requises par l’application pour valider le jeton.


4. L’application valide le jeton à l’aide d’une clé de signature publique et des informations de l’émetteur disponibles dans le document de métadonnées de fédération pour Azure AD. Une fois que l’application valide le jeton, Azure AD démarre une nouvelle session avec l’utilisateur. Cette session permet à l’utilisateur à accéder à l’application jusqu'à ce qu’il arrive à expiration.


#### <a name="code-samples"></a>Exemples de code


Consultez les exemples de code de navigateur Web aux scénarios d’Application Web. Et, à consulter régulièrement--nous ajouter de nouveaux exemples tout le temps. [Navigateur web pour une Application Web](active-directory-code-samples.md#web-browser-to-web-application).


#### <a name="registering"></a>Enregistrement


- Client unique : Si vous créez une application simplement pour votre organisation, il doit être enregistré dans l’annuaire de votre entreprise à l’aide du portail de gestion Azure.


- Client Multiples : Si vous créez une application qui peut être utilisée par les utilisateurs externes à votre organisation, il doit être enregistré dans l’annuaire de votre entreprise, mais également doit être enregistré dans l’annuaire de chaque organisation qui utilisent l’application. Pour rendre votre application disponible dans leur répertoire, vous pouvez inclure un processus d’inscription pour vos clients qui leur permet à votre application. Lors de leur inscription pour votre application, ils seront affichera avec une boîte de dialogue qui indique les autorisations que requises par l’application, puis sur l’option d’autorisation. Selon les autorisations requises, un administrateur de l’autre organisation pourrez être amené à donner l’autorisation séparément. Lorsque l’utilisateur ou administrateur accepte, l’application est enregistrée dans son propre répertoire. Pour plus d’informations, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiration du jeton

La session de l’utilisateur expire à la fin de la durée de vie du jeton émis par Azure AD. Votre application peut raccourcir cet intervalle de temps si vous le souhaitez, telles que la déconnexion d’utilisateurs basés sur une période d’inactivité. À la fin de la session, l’utilisateur devra se connecter à nouveau.





### <a name="single-page-application-spa"></a>Application de Page unique (SPA)

Cette section décrit l’authentification pour une Application de Page unique, qui utilise Azure AD et l’autorisation implicite OAuth 2.0 accorder pour sécuriser son site web mettre fin à l’API précédent. Applications à Page unique sont généralement structurées comme un calque présentation JavaScript (frontal) qui s’exécute dans le navigateur et un API Web serveur principal qui s’exécute sur un serveur et met en œuvre la logique métier de l’application. Pour plus d’informations sur l’autorisation implicite grant et vous aidera à définir si elle est adapté à votre scénario d’application, voir [Présentation du flux de grant implicite oauth2 ne dans Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Dans ce scénario, lorsque l’utilisateur se connecte, le code JavaScript avant-plan fin utilisations [la bibliothèque d’authentification Active Directory pour JavaScript (terme ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) et l’octroi d’autorisations implicites pour obtenir un jeton ID (id_token) à partir d’Azure AD. Le jeton est mis en cache et le client joint à la demande que le jeton PORTEUR lorsque vous faites appel à son API Web principal, qui est sécurisé à l’aide de logiciels OWIN intermédiaires. 
#### <a name="diagram"></a>Diagramme

![Diagramme d’Application de Page unique](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Description du flux de protocole

1. L’utilisateur accède à l’application web.


2. L’application renvoie la frontal JavaScript (couche présentation) dans le navigateur.


3. L’utilisateur lance se connecter, par exemple en cliquant sur une lien de connexion. Le navigateur envoie une commande GET au point de terminaison d’autorisation Azure AD pour demander un jeton ID. Cette requête inclut l’URL ID et réponse client dans les paramètres de requête.


4. Azure AD valide l’URL de réponse par rapport à l’URL de réponse enregistré qui a été configuré dans le portail de gestion Azure.


5. L’utilisateur se connecte sur la page de connexion.


6. Si l’authentification réussit, Azure AD crée un jeton ID et retourne comme un fragment d’URL (#) à l’URL de réponse de l’application. Pour une application de production, l’URL de réponse doit être HTTPS. Le jeton retourné inclut des déclarations à l’utilisateur et Azure AD qui sont requises par l’application pour valider le jeton.


7. Le code de client JavaScript en cours d’exécution dans le navigateur extrait le jeton de la réponse à utiliser dans la sécurisation des appels dans le site web de l’application que mettre fin à l’API précédent.


8. Le navigateur web de l’application API précédent se terminer par le jeton d’accès dans l’en-tête d’autorisation des appels.

#### <a name="code-samples"></a>Exemples de code


Voir les exemples de code pour les scénarios d’Application de Page unique (SPA). Veillez à revérifier fréquemment--nous ajouter de nouveaux exemples tout le temps. [Application de Page unique (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### <a name="registering"></a>Enregistrement


- Client unique : Si vous créez une application simplement pour votre organisation, il doit être enregistré dans l’annuaire de votre entreprise à l’aide du portail de gestion Azure.


- Client Multiples : Si vous créez une application qui peut être utilisée par les utilisateurs externes à votre organisation, il doit être enregistré dans l’annuaire de votre entreprise, mais également doit être enregistré dans l’annuaire de chaque organisation qui utilisent l’application. Pour rendre votre application disponible dans leur répertoire, vous pouvez inclure un processus d’inscription pour vos clients qui leur permet à votre application. Lors de leur inscription pour votre application, ils seront affichera avec une boîte de dialogue qui indique les autorisations que requises par l’application, puis sur l’option d’autorisation. Selon les autorisations requises, un administrateur de l’autre organisation pourrez être amené à donner l’autorisation séparément. Lorsque l’utilisateur ou administrateur accepte, l’application est enregistrée dans son propre répertoire. Pour plus d’informations, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).

Après avoir enregistré l’application, il doit être configuré pour utiliser le protocole Grant implicite OAuth 2.0. Par défaut, ce protocole est désactivé pour les applications. Pour activer le protocole oauth2 n’implicite Grant pour votre application, télécharger son manifeste d’application à partir du portail de gestion Azure, définissez la valeur « oauth2AllowImplicitFlow » sur true et puis téléchargez l’arrière manifeste dans le portail. Pour obtenir des instructions détaillées, voir [Activation OAuth 2.0 implicite accorder pour les Applications de Page unique](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiration du jeton

Lorsque vous utilisez ADAL.js pour gérer l’authentification avec Azure Active Directory, vous bénéficiez de plusieurs fonctionnalités qui facilitent l’actualisation un jeton qui a expiré, ainsi que l’obtention de jetons pour les ressources de l’API web supplémentaires qui peuvent être appelés par l’application. Lorsque l’utilisateur s’authentifie correctement avec Azure AD, une session sécurisée par des cookies est établie pour l’utilisateur entre le navigateur et Azure AD. Il est important de noter que la session existe entre l’utilisateur et Azure AD et non entre l’utilisateur et l’application web s’exécutant sur le serveur. Lorsqu’un jeton arrive à expiration, ADAL.js utilise cette session pour obtenir silencieusement un autre jeton. Pour cela, à l’aide d’un iFrame masqué pour envoyer et recevoir la requête en utilisant le protocole Grant implicite OAuth. ADAL.js pouvez également utiliser ce même mécanisme pour obtenir silencieusement les jetons d’accès à partir d’Azure AD pour d’autres web ressources API que l’application appelle dans la mesure où ces ressources prennent en charge (CORS), le partage des ressources origine croisée est enregistrés dans l’annuaire de l’utilisateur et votre consentement requis a été attribué par l’utilisateur au cours de connexion.


### <a name="native-application-to-web-api"></a>Application native pour API Web


Cette section décrit une application native qui appelle une API web au nom d’un utilisateur. Ce scénario est basé sur le type de licence OAuth 2.0 d’autorisation code avec un client public, comme décrit dans la section 4.1 de la [spécification 2.0 OAuth](http://tools.ietf.org/html/rfc6749). L’application native Obtient un jeton d’accès de l’utilisateur à l’aide du protocole OAuth 2.0. Ce jeton d’accès est ensuite envoyé dans la demande sur le web API, qui autorise l’utilisateur et renvoie la ressource souhaitée.

#### <a name="diagram"></a>Diagramme

![Application native pour Web API diagramme](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Flux d’authentification pour une application native à l’API

#### <a name="description-of-protocol-flow"></a>Description du flux de protocole


Si vous utilisez les bibliothèques d’authentification Active Directory, la plupart des détails du protocole décrites ci-dessous est gérée pour vous, tels que la fenêtre contextuelle navigateur jeton la mise en cache et gestion des jetons d’actualisation.

1. Utiliser un navigateur contextuel, que l’application native effectue une demande au point de terminaison d’autorisation dans Azure Active Directory. Cette requête inclut l’ID de client et la redirection URI de l’application native comme indiqué dans le portail de gestion et l’application URI ID pour le site web API. Si l’utilisateur n’a pas déjà connecté, il est invité à se connecter à nouveau


2. Azure AD authentifie l’utilisateur. Si c’est une application cliente multiples et consentement est requis pour utiliser l’application, l’utilisateur devront consentement s’ils ne l’avez pas déjà fait. Après l’attribution de l’autorisation et si celle-ci est réussie, Azure AD émet une réponse de code autorisation revenir à l’URI de redirection de l’application cliente.


3. Lorsque Azure AD émet une réponse de code autorisation revenir à la redirection URI, l’application cliente s’arrête interaction navigateur et extrait le code d’autorisation de la réponse. À l’aide de ce code d’autorisation, l’application cliente envoie une demande jeton point de terminaison d’Azure AD inclut le code d’autorisation, les détails concernant l’application cliente (ID client et URI de redirection) et la ressource souhaitée (application URI ID pour le site web API).


4. Le code d’autorisation et des informations sur l’API web et une application client sont validées par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT. En outre, Azure AD renvoie des informations de base sur l’utilisateur, tels que son nom et le client les identifiant affichage.


5. Sur HTTPS, l’application cliente utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête d’autorisation de la requête sur le web API. L’API web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.


6. Lorsque le jeton d’accès arrive à expiration, l’application cliente recevront une erreur indiquant que l’utilisateur doit s’authentifier à nouveau. Si l’application a un jeton actualisation valide, il peut être utilisé pour acquérir un nouveau jeton d’accès sans avertir l’utilisateur pour vous connecter à nouveau. Si le jeton d’actualisation arrive à expiration, l’application devra interactive authentifier une nouvelle fois l’utilisateur.


> [AZURE.NOTE] Le jeton actualisation émis par Azure AD peut être utilisé pour accéder à plusieurs ressources. Par exemple, si vous avez une application cliente qui dispose des autorisations nécessaires pour appeler web deux API, le jeton d’actualisation peut servir à obtenir un accès à un jeton pour l’autres API web ainsi.


#### <a name="code-samples"></a>Exemples de code


Voir les exemples de code pour une Application Native aux scénarios d’API Web. Et, à consulter régulièrement--nous ajouter de nouveaux exemples tout le temps. [Application native pour API Web](active-directory-code-samples.md#native-application-to-web-api).


#### <a name="registering"></a>Enregistrement


- Client unique : Natives l’application et le web API doivent être enregistrés dans le même répertoire dans Azure AD. L’API web peut être configuré pour exposer un ensemble d’autorisation, qui sont utilisées pour limiter l’accès de l’application native à ses ressources. L’application cliente puis sélectionne les autorisations souhaitées dans le menu déroulant : « Autorisations pour les autres Applications » dans le portail de gestion Azure.


- Client Multiples : Tout d’abord, l’application native uniquement déjà été enregistré dans le développeur ou d’un répertoire de publisher. Ensuite, l’application native est configurée pour indiquer les autorisations que requises pour être opérationnel. Cette liste des autorisations requises s’affiche dans une boîte de dialogue lorsqu’un utilisateur ou un administrateur dans le répertoire de destination donne consentement pour l’application, qui rend disponible pour leur organisation. Certaines applications requièrent des autorisations uniquement au niveau utilisateur, laquelle tous les utilisateurs de l’organisation pourront accorder aux. Autres applications requièrent des autorisations au niveau de l’administrateur, qui ne peut pas acceptez un utilisateur de l’organisation. Seul l’administrateur du répertoire peut donner son consentement aux applications qui nécessitent ce niveau d’autorisation. Lorsque l’utilisateur ou administrateur accepte, uniquement l’API web est enregistré dans leur répertoire. Pour plus d’informations, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiration du jeton


Lorsque l’application native utilise son code d’autorisation pour obtenir un accès JWT jeton, il reçoit également un jeton d’actualisation JWT. Lorsque le jeton d’accès arrive à expiration, le jeton d’actualisation peut servir à authentifier l’utilisateur sans avoir à vous reconnecter. Ce jeton d’actualisation, est utilisé pour authentifier l’utilisateur, ce qui entraîne un nouveau jeton d’accès et un jeton d’actualisation.





### <a name="web-application-to-web-api"></a>Application Web aux API Web


Cette section décrit une application web qui a besoin d’obtenir des ressources à partir d’un site web API. Dans ce scénario, il existe deux types d’identité que l’application web peut utiliser pour authentifier et appeler l’API web : une identité de l’application ou une identité utilisateur délégué.

*Identité de l’application :* Ce scénario utilise octroi d’informations d’identification OAuth 2.0 client pour authentifier que l’application et accéder au web API. Lorsque vous utilisez une identité de l’application, le site web API uniquement détecte que l’application web appelle, que le site web API ne reçoit pas d’informations sur l’utilisateur. Si l’application reçoit les informations relatives à l’utilisateur, il sera envoyé via le protocole d’application et il n’est pas signé par Azure AD. L’API web suppose que l’application web authentifié l’utilisateur. Pour cette raison, ce modèle est appelé un sous système approuvé.

*Délégués identité de l’utilisateur :* Ce scénario, il est possible de deux façons : OpenID vous connecter et 2.0 OAuth d’octroi de code avec un client confidentiel. L’application web Obtient un jeton d’accès de l’utilisateur, qui s’avère sur le web API que l’utilisateur authentifié avec succès à l’application web et que l’application web a été en mesure d’obtenir une identité utilisateur délégué pour appeler le API web. Ce jeton d’accès est envoyé dans la demande sur le web API, qui autorise l’utilisateur et renvoie la ressource souhaitée.

#### <a name="diagram"></a>Diagramme

![Application Web au diagramme API Web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>Description du flux de protocole

L’identité de l’application et les types d’identité utilisateur délégué sont décrits dans le flux ci-dessous. La différence entre les deux clée est que l’identité utilisateur délégué doit acquérir tout d’abord un code d’autorisation avant que l’utilisateur peut se connecter et accéder à l’API web.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identité de l’application avec le jeton 2.0 Client les informations d’identification Grant

1. Un utilisateur est connecté à Azure AD dans l’application web (voir le [Navigateur Web pour une Application Web](#web-browser-to-web-application) ci-dessus).


2. L’application web doit acquérir un jeton d’accès afin qu’il puisse s’authentifier à l’API web et récupérer la ressource souhaitée. Elle effectue une requête au point de terminaison jeton de Azure AD, fournir les informations d’identification, l’ID de client et application web l’API ID URI.


3. Azure AD authentifie l’application et renvoie un jeton d’accès JWT qui permet d’appeler l’API web.


4. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête d’autorisation de la requête sur le web API. L’API web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

##### <a name="delegated-user-identity-with-openid-connect"></a>Identité utilisateur délégué avec OpenID se connecter

1. Un utilisateur est connecté à une application web à l’aide d’Azure AD (voir la section de [Navigateur Web pour une Application Web](#web-browser-to-web-application) ci-dessus). Si l’utilisateur de l’application web n’a pas encore accepté de l’application web d’appeler l’API web sur son nom, l’utilisateur devra consentement. L’application affiche les autorisations que requises, et si un d'entre eux est autorisations au niveau de l’administrateur, un utilisateur normal dans le répertoire pas pouvoir consentement. Ce processus consentement s’applique uniquement aux applications clients multiples, les applications de client pas unique, comme l’application ont déjà les autorisations nécessaires. Lorsque l’utilisateur connecté, l’application web reçu un jeton ID des informations sur l’utilisateur, ainsi que d’un code d’autorisation.


2. En utilisant le code d’autorisation émanant d’Azure AD, l’application web envoie une demande jeton point de terminaison d’Azure AD inclut le code d’autorisation, les détails concernant l’application cliente (ID client et URI de redirection) et la ressource souhaitée (application URI ID pour le site web API).


3. Le code d’autorisation et des informations sur l’application web et API web sont validées par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.


4. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête d’autorisation de la requête sur le web API. L’API web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identité utilisateur délégué avec Code accorder l’autorisation de OAuth 2.0

1. Un utilisateur est déjà connecté à une application web, dont mécanisme d’authentification est indépendante de Azure AD.


2. L’application web nécessite un code d’autorisation pour acquérir un jeton d’accès afin qu’il émet une demande par le biais du navigateur au point de terminaison de Azure AD d’autorisation, fournir l’ID de client et rediriger URI pour l’application web après une authentification réussie. L’utilisateur se connecte à Azure Active Directory.


3. Si l’utilisateur de l’application web n’a pas encore accepté de l’application web d’appeler l’API web sur son nom, l’utilisateur devra consentement. L’application affiche les autorisations que requises, et si un d'entre eux est autorisations au niveau de l’administrateur, un utilisateur normal dans le répertoire pas pouvoir consentement. Ce processus consentement s’applique uniquement aux applications clients multiples, les applications de client pas unique, comme l’application ont déjà les autorisations nécessaires.


4. Une fois que l’utilisateur a accepté, l’application web reçoit le code d’autorisation dont il a besoin d’acquérir un jeton d’accès.


5. En utilisant le code d’autorisation émanant d’Azure AD, l’application web envoie une demande jeton point de terminaison d’Azure AD inclut le code d’autorisation, les détails concernant l’application cliente (ID client et URI de redirection) et la ressource souhaitée (application URI ID pour le site web API).


6. Le code d’autorisation et des informations sur l’application web et API web sont validées par Azure AD. Si la validation réussit, Azure AD renvoie deux jetons : un jeton d’accès JWT et un jeton d’actualisation JWT.


7. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête d’autorisation de la requête sur le web API. L’API web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

#### <a name="code-samples"></a>Exemples de code

Voir les exemples de code pour une Application Web aux scénarios d’API Web. Et, à consulter régulièrement--nous ajouter de nouveaux exemples tout le temps. Web [Application d’API Web](active-directory-code-samples.md#web-application-to-web-api).


#### <a name="registering"></a>Enregistrement

- Client unique : Pour l’identité de l’application et utilisateur délégué identité cas, l’application web et l’API web doivent être enregistrés dans le même répertoire dans Azure Active Directory. L’API web peut être configuré pour exposer un ensemble d’autorisation, qui sont utilisées pour limiter l’accès de l’application web à ses ressources. Si un type d’identité utilisateur déléguée est utilisé, l’application web a besoin sélectionner les autorisations de votre choix dans le menu déroulant : « Autorisations pour les autres Applications » dans le portail de gestion Azure. Cette étape n’est pas nécessaire si le type d’identité application est utilisé.


- Client Multiples : Tout d’abord, l’application web est configurée pour indiquer les autorisations que requises pour être opérationnel. Cette liste des autorisations requises s’affiche dans une boîte de dialogue lorsqu’un utilisateur ou un administrateur dans le répertoire de destination donne consentement pour l’application, qui rend disponible pour leur organisation. Certaines applications requièrent des autorisations uniquement au niveau utilisateur, laquelle tous les utilisateurs de l’organisation pourront accorder aux. Autres applications requièrent des autorisations au niveau de l’administrateur, qui ne peut pas acceptez un utilisateur de l’organisation. Seul l’administrateur du répertoire peut donner son consentement aux applications qui nécessitent ce niveau d’autorisation. Lorsque l’utilisateur ou administrateur accepte, l’application web et l’API web sont tous deux enregistrés dans leur répertoire.

#### <a name="token-expiration"></a>Expiration du jeton

Lorsque l’application web utilise son code d’autorisation pour obtenir un accès JWT jeton, il reçoit également un jeton d’actualisation JWT. Lorsque le jeton d’accès arrive à expiration, le jeton d’actualisation peut servir à authentifier l’utilisateur sans avoir à vous reconnecter. Ce jeton d’actualisation, est utilisé pour authentifier l’utilisateur, ce qui entraîne un nouveau jeton d’accès et un jeton d’actualisation.


### <a name="daemon-or-server-application-to-web-api"></a>Processus ou Application serveur d’API Web


Cette section décrit une processus ou une application serveur qui a besoin d’obtenir des ressources à partir d’un site web API. Il existe deux scénarios sous-adresse qui s’appliquent à cette section : un processus qui doit effectuer des appels d’un site web API, basé sur OAuth 2.0 client informations d’identification grant type ; et une application de serveur (par exemple, un site web API) qui doit effectuer des appels d’un site web API, basé sur la spécification de brouillon OAuth 2.0 On-Behalf-Of.

Pour le scénario lorsqu’une application de processus a besoin appeler un site web API, il est important de comprendre les choses. Tout d’abord, interaction de l’utilisateur n’est pas possible avec une application de processus, qui nécessite l’application dispose de sa propre identité. Exemple d’une application de processus est un traitement par lots ou un service de système d’exploitation en cours d’exécution en arrière-plan. Ce type d’application demande un jeton d’accès à l’aide de son identité de l’application et son client la présentation ID, les informations d’identification (mot de passe ou certificat) et application URI ID à Azure Active Directory. Après une authentification réussie, le processus reçoit un jeton d’accès à partir d’Azure AD, qui est ensuite utilisé pour appeler le API web.

Pour le scénario lorsqu’une application serveur a besoin appeler un site web API, il est utile d’utiliser un exemple. Imaginons qu’un utilisateur authentifié dans une application native et que cette application native a besoin appeler un API web. Azure AD émet un jeton d’accès JWT pour appeler l’API web. Si l’API web a besoin appeler un autre site web en aval API, il permet le flux sur à la place de déléguer une identité de l’utilisateur et de s’authentifier sur le web de deuxième niveau API.

#### <a name="diagram"></a>Diagramme

![Processus ou une Application serveur au diagramme API Web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Description du flux de protocole

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identité de l’application avec le jeton 2.0 Client les informations d’identification Grant

1. Tout d’abord, l’application serveur doit vous authentifier avec Azure AD en tant que tel, sans intervention humaine comme une boîte de dialogue authentification interactive. Elle effectue une requête au point de terminaison jeton de Azure AD, fournir les informations d’identification, ID de client et URI ID d’application.


2. Azure AD authentifie l’application et renvoie un jeton d’accès JWT qui permet d’appeler l’API web.


3. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête d’autorisation de la requête sur le web API. L’API web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identité de l’utilisateur déléguée avec OAuth 2.0 sur à la place de brouillon spécification

Le flux présenté ci-dessous suppose qu’un utilisateur a été authentifié sur une autre application (par exemple, une application native) et son identité de l’utilisateur a été utilisée pour acquérir un jeton d’accès sur le web de premier niveau API.

1. L’application native envoie le jeton d’accès sur le web de premier niveau API.


2. L’API web de premier niveau envoie une demande de point de terminaison de Azure AD jeton, fournissant son client ID et des informations d’identification, ainsi que des jeton d’accès de l’utilisateur. En outre, la demande est envoyée avec un on_behalf_of paramètre qui indique le web API demande des nouveaux jetons pour appeler un site web en aval API au nom de l’utilisateur d’origine.


3. Azure AD vérifie que le site web de premier niveau API dispose des autorisations pour accéder au site web de deuxième niveau API et valide la demande en retournant un jeton d’accès JWT et un JWT actualiser jeton sur le web de premier niveau API.


4. Sur HTTPS, l’API web de premier niveau appelle ensuite le site web de deuxième niveau API en ajoutant la chaîne jeton dans l’en-tête d’autorisation dans la demande. Le site web de premier niveau API pouvez continuer à appeler le site web de deuxième niveau API dans la mesure où le jeton d’accès et actualisation jetons sont valides.

#### <a name="code-samples"></a>Exemples de code

Voir les exemples de code pour processus ou une Application serveur aux scénarios d’API Web. Et, à consulter régulièrement--nous ajouter de nouveaux exemples tout le temps. [Serveur ou une Application de processus à l’API Web](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Enregistrement

- Client unique : Pour l’identité de l’application et utilisateur délégué identité cas, la processus ou une application serveur doit être inscrit dans le même répertoire dans Azure Active Directory. L’API web peut être configuré pour exposer un ensemble d’autorisation, qui sont utilisés pour limiter le processus ou l’accès à ses ressources du serveur. Si un type d’identité utilisateur déléguée est utilisé, l’application serveur a besoin sélectionner les autorisations de votre choix dans le menu déroulant : « Autorisations pour les autres Applications » dans le portail de gestion Azure. Cette étape n’est pas nécessaire si le type d’identité application est utilisé.


- Client Multiples : Tout d’abord, l’application serveur ou un processus est configurée pour indiquer les autorisations que requises pour être opérationnel. Cette liste des autorisations requises s’affiche dans une boîte de dialogue lorsqu’un utilisateur ou un administrateur dans le répertoire de destination donne consentement pour l’application, qui rend disponible pour leur organisation. Certaines applications requièrent des autorisations uniquement au niveau utilisateur, laquelle tous les utilisateurs de l’organisation pourront accorder aux. Autres applications requièrent des autorisations au niveau de l’administrateur, qui ne peut pas acceptez un utilisateur de l’organisation. Seul l’administrateur du répertoire peut donner son consentement aux applications qui nécessitent ce niveau d’autorisation. Lorsque l’utilisateur ou administrateur accepte, à la fois du site web API enregistrées dans leur répertoire.

#### <a name="token-expiration"></a>Expiration du jeton

Quand la première application utilise son code d’autorisation pour obtenir un accès JWT jeton, il reçoit également un jeton d’actualisation JWT. Lorsque le jeton d’accès arrive à expiration, le jeton d’actualisation peut servir à authentifier l’utilisateur sans demander des informations d’identification. Ce jeton d’actualisation, est utilisé pour authentifier l’utilisateur, ce qui entraîne un nouveau jeton d’accès et un jeton d’actualisation.

## <a name="see-also"></a>Voir aussi

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)

[Exemples de Code Azure Active Directory](active-directory-code-samples.md)

[Informations importantes sur la connexion substitution de la clé Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 dans Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx)
