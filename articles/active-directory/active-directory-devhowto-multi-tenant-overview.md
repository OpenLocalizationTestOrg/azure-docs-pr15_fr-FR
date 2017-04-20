<properties
   pageTitle="Découvrez comment créer une application de se connecter tout utilisateur Azure Active Directory | Microsoft Azure"
   description="Étape par étape des instructions pour la création d’une application de se connecter un utilisateur à partir d’un client Azure Active Directory, également connu sous le nom d’une application cliente multiples."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Comment se connecter tout utilisateur Azure Active Directory (AD) en utilisant le modèle d’application client multiples
Si vous proposez un logiciel comme une application de Service à de nombreuses organisations, vous pouvez configurer votre application pour accepter les connexions à partir d’un client Azure AD.  Dans Azure AD cette option est appelée rendre votre client à plusieurs applications.  Les utilisateurs dans n’importe quel client Azure AD pourront se connecter à votre application après consentez à utiliser leur propre compte avec votre application.  

Si vous avez une application existante qui possède son propre système compte ou prend en charge d’autres types de se connecter à partir d’autres fournisseurs cloud, l’ajout de connexion Azure AD à partir d’un client est aussi simple que l’enregistrement de votre application, l’ajout de signe dans le code via oauth2 ne, OpenID se connecter ou SAML et placer une connexion avec le bouton Microsoft sur votre application. Cliquez sur le bouton ci-dessous pour en savoir plus sur la personnalisation de votre application.

[! [Se connecter bouton] [AAD-connexion]][AAD-App-Branding]


Cet article suppose que vous connaissez déjà création d’une application client unique pour Azure AD.  Si vous n’êtes pas, tête précédent jusqu'à [page d’accueil du guide pour les développeurs] [ AAD-Dev-Guide] et essayez l’un de nos Démarrages rapides !

Il existe quatre étapes simples pour convertir votre application dans une application cliente multiples Azure AD :

1.  Mettre à jour votre inscription d’application pour être client multiples
2.  Mettre à jour votre code pour envoyer des requêtes à la /common point de terminaison 
3.  Mettre à jour votre code permettant de gérer plusieurs valeurs de l’émetteur
4.  Comprendre consentement utilisateur et d’administration et apporter des modifications de code approprié

Nous allons étudier chaque étape en détail. Vous pouvez également accéder directement à [cette liste des exemples de multi-clients][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Mettre à jour l’enregistrement pour être client multiples
Par défaut, des enregistrements d’application/API web dans Azure AD sont client unique.  Vous pouvez apporter votre inscription client multiples en recherchant le commutateur « Application est multi-client » dans la page configuration de l’inscription de votre application dans le [portail classique Azure] [ AZURE-classic-portal] et la valeur « Oui ».

Remarque : Avant d’effectuer une application cliente multiples, Azure AD nécessite l’URI ID application de l’application soit globalement unique. Application ID URI est une des façons qu'une application est identifiée dans les messages de protocole.  Pour une application client unique, il est suffisant pour l’URI ID application être unique au sein de ce client.  Pour une application cliente multiples, il doit être globalement unique pour Azure AD puisse trouver l’application sur toutes les installations.  En demandant l’URI ID application d’avoir un nom d’hôte qui correspond à un domaine vérifié du client Azure AD l’unicité globale est appliquée.  Par exemple, si le nom de votre client a été contoso.onmicrosoft.com puis valide application ID URI serait `https://contoso.onmicrosoft.com/myapp`.  Si votre client a un domaine vérifié de `contoso.com`, puis un URI ID application valide serait également `https://contoso.com/myapp`.  Définition d’une application comme client multiples échouera si l’URI ID application ne respecte pas ce modèle.

Enregistrements native client sont multi-clients par défaut.  Vous n’avez pas besoin d’aucune action pour rendre un native multi-client client application d’inscription.

## <a name="update-your-code-to-send-requests-to-common"></a>Mettre à jour votre code pour envoyer des requêtes à /common
Dans une application client unique, se connecter demandes est envoyées au point de terminaison de connexion du client.   Par exemple, pour contoso.onmicrosoft.com le point de terminaison serait :

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Requêtes envoyées au point de terminaison d’un client de se connecter aux utilisateurs (ou invités) dans ce client aux applications de ce client.  Avec une application cliente multiples, l’application ne sait avance quel client proviennent de l’utilisateur, afin que vous ne pouvez pas envoyer des demandes au point de terminaison d’un client.  En revanche, les demandes sont envoyées à un point de terminaison multiplexage via AD Azure tous les clients :

    https://login.microsoftonline.com/common

Lorsque Azure AD reçoit une demande sur la /common point de terminaison, il se connecte l’utilisateur et par conséquent découvre quel client provient de l’utilisateur.  La/point de terminaison courantes fonctionne avec toutes les protocoles d’authentification pris en charge par Azure AD : OpenID se connecter, OAuth 2.0, SAML 2.0 et la fédération Web.

La réponse à l’application, puis connectez-vous contient un jeton représentant l’utilisateur.  La valeur de l’émetteur du jeton indique une application quel client provient de l’utilisateur.  Lorsqu’une réponse renvoie provenant point de terminaison, la valeur de l’émetteur du jeton correspondra au client de l’utilisateur.  Il est important de noter les /common point de terminaison n’est pas un client et n’est pas un émetteur, il est juste multiplexage.  Lorsque vous utilisez /common, la logique dans votre application pour valider les jetons doit être mis à jour pour prendre en compte. 

Comme mentionné précédemment, les applications clients multiples doivent également fournir une expérience de connexion cohérente pour les utilisateurs, après l’application Azure AD instructions de personnalisation. Cliquez sur le bouton ci-dessous pour en savoir plus sur la personnalisation de votre application.

[! [Se connecter bouton] [AAD-connexion]][AAD-App-Branding]

Examinons l’utilisation de la /common point de terminaison et votre implémentation du code plus en détail.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Mettre à jour votre code permettant de gérer plusieurs valeurs de l’émetteur
Les applications Web et web API recevoir et valider les jetons à partir d’Azure AD.  

> [AZURE.NOTE] Tandis que les applications clientes native demandent et recevoir des jetons à partir d’Azure AD, ils faire pour envoyer à l’API, où ils sont validées.  Applications natives ne pas valident les jetons et doivent les traiter comme opaque.

Examinons à comment une application valide jetons il reçoit à partir d’Azure AD.  Une application client unique prendra en règle générale, une valeur de point de terminaison comme :

    https://login.microsoftonline.com/contoso.onmicrosoft.com

et utilisez-le pour créer une URL de métadonnées (dans ce cas, OpenID se connecter) comme :

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Pour télécharger les deux types d’informations critiques qui sont utilisés pour valider les jetons : le client de signature clés et la valeur de l’émetteur.  Chaque client Azure AD comporte une valeur unique de l’émetteur du formulaire :

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

où la valeur GUID est la version adaptée aux sites changement de nom de l’ID client du client.  Si vous cliquez sur le lien de métadonnées ci-dessus pour `contoso.onmicrosoft.com`, vous pouvez voir cette valeur émetteur dans le document.

Lorsqu’une application client unique valide un jeton, il vérifie la signature du jeton contre les clés de signature dans le document de métadonnées et vérifie la valeur de l’émetteur du jeton correspond à celui qui a été trouvée dans le document de métadonnées.

Depuis la /common point de terminaison ne correspond pas à un client et n’est pas un émetteur, lorsque vous examinez la valeur de l’émetteur dans les métadonnées pour/courantes il dispose d’une URL basé sur un modèle à la place d’une valeur réelle :

    https://sts.windows.net/{tenantid}/

Par conséquent, une application cliente multiples ne peut pas valider jetons en correspondant à la valeur de l’émetteur dans les métadonnées avec la `issuer` valeur du jeton.  Une application cliente multiples doit logique décide ne pas les valeurs de l’émetteur sont valides et qui sont, basé sur le client partie de l’ID de la valeur de l’émetteur.  

Par exemple, si une application cliente multiple autorise uniquement se connecter à partir de clients spécifiques inscrit pour leur service, il doit vérifier soit la valeur de l’émetteur ou le `tid` réclamer la valeur du jeton pour vous assurer que ce client est dans sa liste d’abonnés.  Si une application cliente multiples uniquement traite des individus et ne fait pas appel les décisions d’accès basées sur les clients, il peut ignorer la valeur de l’émetteur complètement.

Dans les exemples de multi-clients vous trouverez dans la section [Contenu associé](#related-content) à la fin de cet article, l’émetteur validation est désactivée pour permettre à n’importe quel client Azure AD pour vous connecter.

Maintenant examinons l’expérience utilisateur pour les utilisateurs sont se connecter aux applications clients multiples.

## <a name="understanding-user-and-admin-consent"></a>Utilisateur compréhension et consentement d’administration
Pour un utilisateur pour vous connecter à une application dans Azure AD, l’application doit être représentée dans le client de l’utilisateur.  Cela permet à l’organisation faire appliquer des stratégies uniques lorsque les utilisateurs de leur client se connecter à l’application.  Pour une application client unique cet enregistrement est simple ; Il est celui que se passe-t-il lorsque vous enregistrez l’application dans le [portail classique Azure][AZURE-classic-portal].

Pour une application cliente multiples, l’enregistrement initial de l’application se trouve dans le client Azure AD utilisé par le développeur.  Lorsqu’un utilisateur travaillant dans un autre client se connecte à l’application pour la première fois, Azure AD vous invite à indiquer qu’elles acceptent les autorisations requises par l’application.  Si elles d’accord, puis une représentation de l’application appelée *service principal* est créée dans le client de l’utilisateur, et peut continuer à se connecter. Une délégation est également créée dans le répertoire enregistrant consentement de l’utilisateur à l’application. Voir les [objets d’Application et objets Principal du Service] [ AAD-App-SP-Objects] pour plus d’informations sur l’application objets Application et ServicePrincipal, et comment ils sont liés entre eux.

![Consentement pour l’application à un niveau][Consent-Single-Tier] 

Cette expérience consentement est affectée par les autorisations requises par l’application.  Azure AD prend en charge les deux types d’autorisations et application et déléguées :

- Une autorisation déléguée accorde à une application la possibilité d’agir en tant qu’un utilisateur connecté pour un sous-ensemble des choses que l’utilisateur peut faire.  Par exemple, vous pouvez accorder une application l’autorisation déléguée pour lire le calendrier de l’utilisateur connecté.
- Une application seule autorisation directement à l’identité de l’application.  Par exemple, vous pouvez accorder l’autorisation application uniquement lire la liste des utilisateurs d’un client une application, et il sera en mesure d’effectuer cette opération que qui est connecté à l’application.

Certaines autorisations peuvent être acceptées par un utilisateur régulier, tandis que d’autres requièrent consentement d’un administrateur client. 

### <a name="admin-consent"></a>Consentement d’administration
Autorisations application seule nécessitent toujours consentement d’un administrateur client.  Si votre application demande une autorisation d’application seule et un utilisateur normal essaie de se connecter à l’application, votre application obtenez un message d’erreur indiquant que l’utilisateur ne parvient pas à consentement.

Certaines autorisations déléguées nécessitent également le consentement d’un administrateur client.  Par exemple, la possibilité de répondre à Azure Active Directory en tant que l’utilisateur connecté nécessite consentement d’un administrateur client.  Comme autorisations application uniquement, si un utilisateur ordinaire essaie de se connecter à une application demandant une autorisation déléguée qui nécessite consentement administrateur, votre application reçoit une erreur.  Si une autorisation requiert ou non d’administration consentement est déterminé par le développeur qui a publié la ressource et se trouve dans la documentation de la ressource.  Des liens vers des rubriques qui décrivent les autorisations disponibles pour l’API de Azure AD Graph et Microsoft Graph se trouvent dans la section [Contenu associé](#related-content) de cet article.

Si votre application utilise des autorisations qui nécessitent consentement d’administration, vous devez avoir un mouvement dans votre application tel qu’un bouton ou le lien où l’administrateur peut initier l’action.  La demande de votre application envoie pour cette action est une demande d’autorisation habituelle OpenID/oauth2 ne se connecter, mais qui comprend également le `prompt=admin_consent` paramètre de chaîne de requête.  Une fois que l’administrateur a accepté et l’entité de service est créée dans le client du client, demandes de connexion ultérieures n’est pas nécessaire la `prompt=admin_consent` paramètre.   Étant donné que l’administrateur a décidé que les autorisations demandées sont acceptables, aucun autre utilisateur dans le client ne devra de consentement à partir de là.

La `prompt=admin_consent` paramètre peut également être utilisé par les applications qui demandent des autorisations qui ne nécessitent pas consentement d’administration, mais souhaitez donner une expérience où l’administrateur client » s’inscrit » pour l’application une seule fois, et aucun autre utilisateur n’êtes invité au consentement à partir de là sur.

Si une application requiert consentement administrateur, et que l’administrateur se connecte à l’application, mais la `prompt=admin_consent` paramètre n’est pas envoyé, l’administrateur ne pourrez pas correctement consentement pour l’application, mais ils seront uniquement consentement pour leur compte d’utilisateur.  Les utilisateurs classiques pas seront toujours en mesure de se connecter et consentez à l’application.  Ceci est utile si vous voulez permettre l’administrateur client pour Explorer votre application avant d’autoriser l’accès d’autres utilisateurs.

Un administrateur client peut désactiver la possibilité pour les utilisateurs réguliers à consentement pour les applications.  Si cette fonctionnalité est désactivée, administrateur accord est toujours requis pour l’application à être configuré dans le client.  Si vous souhaitez tester votre application avec consentement utilisateur régulier désactivé, vous pouvez trouver le commutateur de configuration dans le client Azure AD section configuration du [portail classique Azure][AZURE-classic-portal].

> [AZURE.NOTE] Certaines applications souhaite une expérience où les utilisateurs classiques sont en mesure de consentement initiale, et plus loin l’application peut impliquer les autorisations d’administrateur et demande nécessitant consentement d’administration.  Il n’existe aucun moyen d’effectuer cette opération avec un enregistrement unique application dans Azure AD aujourd'hui.  Les prochains point de terminaison Azure AD v2 permettra applications pour demander des autorisations en cours d’exécution, et non au moment de l’enregistrement, qui permettra à ce scénario.  Pour plus d’informations, voir le [Guide du développeur v2 modèle d’applications Azure AD][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Applications consentement et à plusieurs niveaux
Votre application peut avoir plusieurs niveaux, chacun représenté par sa propre inscription dans Azure Active Directory.  Par exemple, une application native qui appelle une API web ou une application web qui appelle une API web.  Dans les deux cas, le client (application native ou web app) demande des autorisations pour appeler la ressource (API web).  Pour le client pour être accepté correctement dans le client d’un client, toutes les ressources auquel il demande d’autorisations doivent déjà exister dans client du client.  Si cette condition n’est pas remplie, Azure AD renverra une erreur que la ressource doit être d’abord ajoutée.

Cela peut être un problème si votre application logique se compose de deux ou plusieurs application des enregistrements, par exemple un client distinct et des ressources.  Comment pouvez-vous la ressource dans le client client première ?  Azure AD couvre ce cas en activant le client et des ressources être accepté en une seule étape, où l’utilisateur voit la somme des autorisations demandées par le client et les ressources dans la page consentement.  Pour activer ce comportement, inscription d’application de la ressource doit inclure l’application ID client comme un `knownClientApplications` dans son manifeste d’application.  Par exemple :

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Cette propriété peut être mis à jour par le biais de la ressource [manifeste de l’application][AAD-App-Manifest]et est présenté dans un client natif à plusieurs niveaux en appelant exemples d’API web dans la section [Contenu associé](#related-content) à la fin de cet article. L’illustration ci-dessous fournit une vue d’ensemble du consentement pour une application à plusieurs niveaux :

![Consentement pour l’application client connus à plusieurs niveaux][Consent-Multi-Tier-Known-Client] 

Un cas semblable se passe-t-il si les différents niveaux d’une application sont enregistrés dans les clients différents.  Par exemple, considérez le cas de la création d’une application cliente native qui appelle l’API en ligne Office 365 Exchange.  Pour développer la natif application et versions ultérieur pour l’application native pour s’exécuter en client d’un client, l’entité du service Exchange Online doit être présente.  Dans ce cas, le client doit acheter Exchange Online pour le service principal devant être créé dans leur client.  Dans le cas d’API créée par une organisation autre que Microsoft, le développeur de l’API doit fournir une méthode pour leurs clients de consentement leur application dans un client de client, par exemple une page web que les lecteurs consentement en utilisant les mécanismes décrites dans cet article.  Une fois l’entité de service est créée dans le client, l’application native accessible jetons de l’API.

L’illustration ci-dessous fournit une vue d’ensemble du consentement pour une application à plusieurs niveaux enregistrée dans différents clients :

![Consentement à application plusieurs à plusieurs niveaux][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Révocation d’une autorisation
Les utilisateurs et les administrateurs peuvent révoquer consentement à votre application à tout moment :

- Utilisateurs révoquer l’accès aux applications individuelles en supprimant leurs [Applications du Panneau de configuration Access] [ AAD-Access-Panel] liste.
- Les administrateurs révoquer l’accès aux applications en supprimant Azure AD à l’aide de la section Gestion Azure AD du [portail classique Azure][AZURE-classic-portal].

Si un administrateur accepte à l’application pour tous les utilisateurs dans un client, les utilisateurs ne peuvent pas révoquer l’accès individuellement.  Seul l’administrateur peut révoquer l’accès et uniquement pour l’ensemble de l’application.

### <a name="consent-and-protocol-support"></a>Consentement et protocoles pris en charge
Consentement est pris en charge dans Azure AD via le jeton OpenID à vous connecter, la fédération Web et les protocoles SAML.  Les protocoles SAML et Web fédération ne pas en charge la `prompt=admin_consent` paramètre, afin d’administration consentement n’est possible que via OAuth et OpenID se connecter.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Mise en cache des jetons d’accès et Applications clients multiples
Applications clients multiples peuvent également obtenir des jetons d’accès pour appeler des API qui est protégés par Azure AD.  Une erreur courante lors à l’aide de la bibliothèque terme (Active Directory authentification ADAL) avec une application cliente multiples consiste à initialement demander un jeton pour un utilisateur à l’aide de /common, recevoir une réponse, puis demandez un jeton suivant pour cet utilisateur même également à l’aide de /common.  Étant donné que la réponse à partir d’Azure AD provient d’un client, pas/courantes, terme ADAL met en mémoire cache du jeton comme étant à partir du client. L’entrée du cache d’échecs le prochain appel à /common pour obtenir un jeton d’accès pour l’utilisateur et l’utilisateur est invité à se connecter à nouveau.  Pour éviter de manquer le cache, vérifiez que les appels suivants pour un utilisateur déjà connecté sont apportées au point de terminaison de client.

## <a name="related-content"></a>Contenu associé

- [Exemples d’application client multiples][AAD-Samples-MT]
- [Instructions de personnalisation pour les Applications][AAD-App-Branding]
- [Guide du développeur Azure AD][AAD-Dev-Guide]
- [Objets de l’application et Principal du Service][AAD-App-SP-Objects]
- [Intégration d’Applications avec Azure Active Directory][AAD-Integrating-Apps]
- [Vue d’ensemble de l’infrastructure d’autorisation][AAD-Consent-Overview]
- [Microsoft Graph API d’autorisation étendues][MSFT-Graph-AAD]
- [Azure AD Graph API d’autorisation étendues][AAD-Graph-Perm-Scopes]

Utilisez la section commentaires Disqus ci-dessous pour fournir des commentaires et aidez-nous à affiner et mettre en forme notre contenu.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














