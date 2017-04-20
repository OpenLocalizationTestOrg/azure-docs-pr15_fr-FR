<properties 
   pageTitle="Authentification Active Directory et le Gestionnaire de ressources | Microsoft Azure"
   description="Guide du développeur à l’authentification avec l’API du Gestionnaire de ressources Azure et Active Directory pour l’intégration d’une application avec d’autres abonnements Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>Comment utiliser Azure Active Directory et le Gestionnaire de ressources pour gérer les ressources d’un client

## <a name="introduction"></a>Introduction

Si vous êtes développeur de logiciels qui ont besoin de créer une application qui gère les ressources Azure du client, cette rubrique vous montre comment vous authentifier avec l’API du Gestionnaire de ressources Azure et accéder aux ressources dans les autres abonnements. 

Votre application peut accéder à l’API Gestionnaire de ressources dans de deux façons :

1. **Utilisateur + access application**: pour les applications qui accèdent aux ressources au nom d’un utilisateur connecté. Cette approche fonctionne pour les applications telles que des applications web et les outils de ligne de commande, traitent uniquement « gestion interactive » d’Azure ressources.
1. **Accès application uniquement**: pour les applications qui s’exécutent les services de processus et les tâches planifiées. Identité de l’application est accordée un accès direct aux ressources. Cette approche fonctionne pour les applications nécessitant à long terme « accès hors connexion » à Azure.

Cette rubrique fournit des instructions détaillées pour créer une application qui utilise ces deux méthodes d’autorisation. Il montre comment effectuer chaque étape avec l’API REST ou c#. L’application ASP.NET MVC complète est disponible dans [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Tout le code de cette rubrique s’exécute comme une application web que vous pouvez essayer en [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense). 

## <a name="what-the-web-app-does"></a>Que signifie l’application web

L’application web :

1. Signes-dans un utilisateur Azure.
2. Demandant à un utilisateur pour accorder l’accès de l’application web au Gestionnaire de ressources.
3. Obtient utilisateur + jeton d’accès de l’application pour accéder au Gestionnaire de ressources.
4. Utilise jeton (de l’étape 3) pour appeler le Gestionnaire de ressources et affecter principal du service de l’application à un rôle dans l’abonnement, ce qui donne l’accès à long terme application à l’abonnement.
5. Obtient un accès application uniquement jeton.
6. Utilise jeton (à l’étape 5) pour gérer les ressources de l’abonnement par le biais du Gestionnaire de ressources.

Voici le flux de bout en bout de l’application web.

![Flux d’authentification du Gestionnaire de ressources](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

En tant qu’utilisateur, vous fournir l’id de l’abonnement pour l’abonnement que vous voulez utiliser :

![fournir des id de l’abonnement](./media/resource-manager-api-authentication/sample-ux-1.png)

Sélectionnez le compte à utiliser pour la connexion.

![Sélectionnez compte](./media/resource-manager-api-authentication/sample-ux-2.png)

Fournir vos informations d’identification.

![fournir des informations d’identification](./media/resource-manager-api-authentication/sample-ux-3.png)

Accorder l’accès de l’application à vos abonnements Azure :
 
![Accorder l’accès](./media/resource-manager-api-authentication/sample-ux-4.png)
 
Gérer vos abonnements connectés :

![Se connecter abonnement](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>Enregistrement de l’application

Avant de commencer à coder, enregistrez votre application web avec Azure Active Directory (AD). L’inscription de l’application crée une identité centrale pour votre application dans Azure Active Directory. Il conserve des informations de base sur votre application, tels que votre application utilise pour authentifier et Azure Gestionnaire de ressources API d’accès aux données d’identification, ID de Client OAuth et URL de réponse. L’inscription de l’application enregistre également les différentes autorisations déléguées nécessaires à votre application lorsque vous accédez à Microsoft APIs au nom de l’utilisateur. 

Étant donné que votre application accède à autre abonnement, vous devez le configurer comme une application cliente multiples. Pour effectuer le contrôle, fournissent un domaine associé à votre annuaire Active Directory. Pour afficher les domaines associés à votre annuaire Active Directory, connectez-vous au [portail classique](https://manage.windowsazure.com). Sélectionnez votre Active Directory, puis **domaines**.

L’exemple suivant montre comment enregistrer l’application à l’aide de PowerShell Azure. Vous devez disposer de la dernière version (août 2016) d’Azure PowerShell pour cette commande. 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
Pour vous connecter en tant que l’application Active Directory, vous devez l’id de l’application et le mot de passe. Pour afficher l’id de l’application qui est renvoyé à partir de la commande précédente, utilisez :

    $app.ApplicationId

L’exemple suivant montre comment enregistrer l’application à l’aide Azure infrastructure du langage commun. 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Les résultats incluent l’identificateur dont vous aurez besoin lors de l’authentification en tant que l’application.

### <a name="optional-configuration---certificate-credential"></a>Configuration facultative - informations d’identification de certificat

Azure AD également prend en charge les informations d’identification de certificat pour les applications : vous créez un certificat auto-signé, conserver la clé privée et ajouter la clé publique à votre inscription application Azure AD. Pour l’authentification, votre application envoie une petite surcharge à AD Azure connecté à l’aide de votre clé privée et Azure AD valide la signature à l’aide de la clé publique que vous avez enregistré.

Pour plus d’informations sur la création d’une application AD avec un certificat, voir [Utiliser Azure PowerShell pour créer un service principal pour accéder aux ressources](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) ou [Infrastructure du langage commun pour créer un service principal pour accéder aux ressources Azure utilisation](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate).

## <a name="get-tenant-id-from-subscription-id"></a>Obtenir des id de client à partir d’id de l’abonnement

Pour demander un jeton qui peut être utilisé pour appeler le Gestionnaire de ressources, votre application doit connaître l’ID client du client AD Azure qui héberge l’abonnement Azure. Il est probable vos utilisateurs connaissent leur ID d’abonnement, mais ils ne savent ne peut-être pas leur client ID pour Active Directory. Pour obtenir l’id d’utilisateur client, demandez à l’utilisateur pour l’id de l’abonnement. Lorsque vous envoyez une demande de l’abonnement, fournissent des id de l’abonnement :

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

La requête échoue, car l’utilisateur n’a pas encore connecté, mais vous pouvez extraire l’id de client à partir de la réponse. Dans cette exception, récupérer l’id de client à partir de la valeur d’en-tête de réponse pour **Authentifier WWW**. Vous voyez cette implémentation de la méthode [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obtenir utilisateur + jeton d’accès de l’application

Votre application redirige l’utilisateur vers Azure AD avec un jeton 2.0 autoriser demande - pour authentifier les informations d’identification de l’utilisateur et revenir à un code d’autorisation. Votre application utilise le code d’autorisation pour obtenir un accès à un jeton pour le Gestionnaire de ressources. La méthode [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) crée la demande d’autorisation.

Cette rubrique indique les demandes d’API REST pour authentifier l’utilisateur. Vous pouvez également utiliser des bibliothèques d’assistance pour effectuer une authentification dans votre code. Pour plus d’informations sur ces bibliothèques, voir [Azure Active Directory authentification bibliothèques](./active-directory/active-directory-authentication-libraries.md). Pour des instructions sur l’intégration de la gestion des identités dans une application, voir le [guide du développeur Azure Active Directory](./active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Demandes d’authentification (OAuth 2.0)

Émettre un Open se connecter/OAuth2.0 autorisez les demandes d’ID au point de terminaison Azure AD autoriser :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Les paramètres de chaîne de requête qui sont disponibles pour cette requête sont décrites dans la rubrique [demande un code d’autorisation](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

L’exemple suivant montre comment demander l’autorisation de OAuth2.0 :

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD authentifie l’utilisateur et, si nécessaire, demande à l’utilisateur pour accorder l’autorisation à l’application. Elle renvoie le code d’autorisation à l’URL de réponse de votre application. Selon la response_mode demandées, Azure AD soit renvoie les données dans la chaîne de requête ou en tant que billet de données.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Demande AUTH (Open ID de connexion)

Si vous le souhaitez non seulement accéder au Gestionnaire de ressources de Azure au nom de l’utilisateur, mais également autorisez l’utilisateur à se connecter à votre application à l’aide de leur compte Azure AD, émettre un Open connecter autorisez les demandes d’ID de. Avec Open ID de connexion, votre application reçoit également un id_token à partir d’Azure AD que votre application peut utiliser pour vous connecter à l’utilisateur.

Les paramètres de chaîne de requête qui sont disponibles pour cette requête sont décrites dans la rubrique [Envoyer la demande de connexion](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) .

Un exemple de demande de connexion ID ouvert est la suivante :

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD authentifie l’utilisateur et, si nécessaire, demande à l’utilisateur pour accorder l’autorisation à l’application. Elle renvoie le code d’autorisation à l’URL de réponse de votre application. Selon la response_mode demandées, Azure AD soit renvoie les données dans la chaîne de requête ou en tant que billet de données.

Exemple ouvrir ID connecter réponse est la suivante :

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Demande de jeton (OAuth2.0 Code Grant flux)

À présent que votre application a reçu le code d’autorisation à partir d’Azure AD, il est temps pour obtenir le niveau d’accès jeton pour le Gestionnaire de ressources Azure.  Publier un OAuth2.0 Code Grant jeton demander au point de terminaison Azure AD jeton : 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Les paramètres de chaîne de requête qui sont disponibles pour cette requête sont décrites dans la rubrique [utiliser le code d’autorisation](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

L’exemple suivant montre une demande de jeton de grant code avec les informations d’identification de mot de passe :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Lorsque vous travaillez avec des informations d’identification du certificat, créez un jeton JSON Web (JWT) et se (RSA SHA256) à l’aide de la clé privée des informations d’identification du certificat de votre application. Les types de revendications pour le jeton sont représentées dans [revendications des jetons JWT](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims). Pour référence, voir le [code Active Directory Auth bibliothèque (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) se jetons Client Assertion JWT.

Consultez les [spécifications Open ID de connexion](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) pour plus d’informations sur l’authentification du client. 

L’exemple suivant montre une demande de jeton de grant code avec les informations d’identification du certificat :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Une réponse d’exemple pour le code accorder jeton : 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Gérer la réponse jeton octroi du code

Une réponse jeton réussie contient (utilisateur + application) pour le Gestionnaire de ressources Azure jeton d’accès. Votre application utilise ce jeton d’accès pour accéder au Gestionnaire de ressources au nom de l’utilisateur. La durée de vie des jetons d’accès émis par Azure AD est d’une heure. Il est probable que votre application web doit renouveler (utilisateur + application) jeton d’accès. S’il faut renouveler le jeton d’accès, utilisez le jeton d’actualisation qui reçoit votre application dans la réponse jeton. Publiez une demande jeton OAuth2.0 au point de terminaison Azure AD jeton : 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Les paramètres à utiliser avec la demande d’actualisation sont décrites dans [l’actualisation du jeton d’accès](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

L’exemple suivant montre comment utiliser l’actualisation jeton :

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Bien que les jetons d’actualisation peuvent être utilisées pour accéder à nouveau jetons d’accès pour le Gestionnaire de ressources Azure, ils ne conviennent pas pour l’accès hors connexion à votre application. La durée de vie des jetons actualisation est limitée et actualisation jetons sont liés à l’utilisateur. Si l’utilisateur quitte l’entreprise, l’application en utilisant le jeton d’actualisation perd l’accès. Cette approche n’est pas adaptée aux applications qui sont utilisées par les équipes pour gérer leurs ressources Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Vérifier si utilisateur peut affecter l’accès à l’abonnement

Votre application maintenant possède un jeton pour accéder au Gestionnaire de ressources de Azure au nom de l’utilisateur. L’étape suivante consiste à connecter votre application à l’abonnement. Une fois connecté, votre application peut gérer ces abonnements même lorsque l’utilisateur n’est pas présenter (l’accès hors connexion à long terme). 

Pour chaque abonnement pour vous connecter, appelez les [autorisations de liste Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn906889.aspx) API pour déterminer si l’utilisateur a gestion des droits d’accès de l’abonnement.

La méthode [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) de l’application exemple ASP.NET MVC met en œuvre cet appel.

L’exemple suivant montre comment demander des autorisations d’un utilisateur sur un abonnement. 83cfe939-2402-4581-b761-4f59b0a041e4 est l’id de l’abonnement.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Un exemple de la réponse pour obtenir les autorisations de l’utilisateur lors de l’abonnement est la suivante :

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Les autorisations API renvoie plusieurs autorisations. Chaque autorisation se compose des actions autorisées (actions) et ne sont pas autorisées actions (notactions). Si une action est présent dans la liste d’actions autorisées de toutes les autorisations et ne figurent pas dans la liste notactions de cette autorisation, l’utilisateur est autorisé à effectuer cette action. **Microsoft.Authorization/RoleAssignments/Write** correspond à l’action que qui autorise l’accès des droits de gestion. Votre application doit analyser le résultat d’autorisations pour rechercher une correspondance regex sur cette chaîne d’action dans les actions et notactions de chacune des autorisations.

## <a name="get-app-only-access-token"></a>Obtenir un accès application uniquement jeton

À présent, vous savez si l’utilisateur peut affecter l’accès à l’abonnement Azure. Étapes suivantes sont :

1. Attribuer le rôle RBAC approprié à l’identité de votre application sur l’abonnement.
2. Valider l’affectation access en recherchant d’autorisation de l’Application de l’abonnement ou en accédant au Gestionnaire de ressources à l’aide du jeton d’application uniquement.
1. Enregistrer la connexion dans votre structure de données applications « abonnements connecté » - conserver l’id de l’abonnement.

Examinons de plus près la première étape. Pour attribuer le rôle RBAC approprié à l’identité l’application, vous devez déterminer :

- Id d’objet de l’identité de votre application dans Azure Active Directory l’utilisateur
- L’identificateur de ce rôle RBAC requis par votre application de l’abonnement

Lorsque votre application authentifie un utilisateur à partir d’une annonce Azure, il crée un objet principal de service pour votre application dans cette Azure AD. Azure permet de rôles RBAC doivent être affectées aux entités de service pour accorder l’accès direct aux applications correspondants sur les ressources Azure. Cette action est exactement ce que nous souhaitons. Requête l’API Azure AD Graph pour déterminer l’identificateur de l’entité de service de votre application dans l’utilisateur connecté est Azure AD.

Vous avez uniquement un jeton d’accès pour le Gestionnaire de ressources Azure : vous avez besoin d’un nouveau jeton d’accès d’appeler l’API Azure AD Graph. Chaque application dans Azure Active Directory est autorisé à son propre objet principal de service, de la requête, un jeton d’accès application seule est suffisant.

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obtenir l’application seule accès jeton pour API Azure AD Graph

Pour authentifier votre application et obtenir un jeton à Azure AD Graph API, émettre une demande de jeton de flux Client d’informations d’identification Grant OAuth2.0 au point de terminaison jeton Azure AD (**https://login.microsoftonline.com/ {directory_domain_name} / oauth2 ne/jeton**).

La méthode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) de l’exemple d’application ASP.net MVC Obtient un accès application seule jeton pour API de graphique à l’aide de la bibliothèque d’authentification Active Directory pour .NET.

Les paramètres de chaîne de requête qui sont disponibles pour cette requête sont décrites dans la rubrique [demande un jeton d’accès](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) .

Un exemple de demande d’informations d’identification client accorder jeton : 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Une réponse d’exemple pour les informations d’identification client accorder jeton : 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtenir ObjectId de principal de service d’application dans Azure AD de l’utilisateur

À présent, utilisez le jeton d’accès de l’application uniquement pour interroger les [identités de Service Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API pour déterminer l’Id d’objet du service de l’application principal dans l’annuaire.

La méthode [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) de l’exemple d’application ASP.net MVC met en œuvre cet appel.

L’exemple suivant montre comment demander principal de service d’une application. a0448380-c346-4f9f-b897-c18733de9394 est l’id de client de l’application.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

L’exemple suivant montre une réponse à la demande de service d’une application principal 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obtenir l’identificateur de rôle RBAC Azure

Pour attribuer le rôle RBAC approprié à votre service principal, vous devez déterminer l’identificateur de ce rôle RBAC Azure.

Le rôle RBAC approprié pour votre application :

- Si votre application surveille uniquement l’abonnement, sans apporter de modifications, il ne nécessite que les autorisations de lecture sur l’abonnement. Attribuer le rôle de **lecteur** .
- Si votre application gère Azure l’abonnement, création, modification/suppression entités, elle nécessite une des autorisations de collaboration.
  - Pour gérer un type particulier de ressource, attribuer les rôles spécifiques aux ressources collaboration (collaboration Machine virtuelle, collaboration réseau virtuel, collaboration de compte de stockage, etc.)
  - Pour gérer n’importe quel type de ressource, attribuer le rôle de **collaborateur** .

L’attribution de rôle pour votre application est visible aux utilisateurs, activez l’obligatoire moindre privilège.

Appeler la [définition de rôle de gestionnaire de ressources API](https://msdn.microsoft.com/library/azure/dn906879.aspx) pour répertorier tous les rôles RBAC Azure et recherche puis parcourir le résultat pour rechercher la définition de rôle souhaité par nom.

La méthode [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) de l’application exemple ASP.net MVC met en œuvre cet appel.

L’exemple de requête suivant montre comment obtenir un identificateur rôle RBAC Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb est l’id de l’abonnement.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

La réponse est au format suivant : 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Vous n’avez pas besoin d’appeler cette API de manière continue. Une fois que vous avez déterminé le GUID connu de la définition de rôle, vous pouvez construire l’id de définition de rôle en tant que :

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Voici les GUID connus des rôles intégrés fréquemment utilisés :

| Rôle | GUID |
| ----- | ------ |
| Reader | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Collaboration | b24988ac-6180-42a0-ab88-20f7382dd24c
| Machine virtuelle collaborateur | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Collaboration réseau virtuel | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Collaboration de compte de stockage | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Collaboration de site Web | de139f84-1756-47ae-9be6-808fbbe84772
| Web Plan de collaboration | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server collaborateur | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| Collaboration de base de données SQL | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>Attribuer un rôle RBAC à application

Vous devez tout ce que vous voulez attribuer le rôle RBAC approprié à votre service principal à l’aide du [Gestionnaire de ressources créer attribution de rôle](https://msdn.microsoft.com/library/azure/dn906887.aspx) API.

La méthode [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) de l’application exemple ASP.net MVC met en œuvre cet appel.

Un exemple de demande pour attribuer un rôle RBAC à application : 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Dans la demande, les valeurs suivantes sont utilisées :

| GUID | Description |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | l’id de l’abonnement
| c3097b31-7309-4c59-b4e3-770f8406bad2 | id d’objet de l’entité de service de l’application
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | l’id du rôle Lecteur
| 4f87261d-2816-465d-8311-70a27558df4c | un nouveau guid créé pour la nouvelle affectation de rôles

La réponse est au format suivant : 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obtenir un accès application uniquement jeton pour le Gestionnaire de ressources Azure

Pour valider cette application a souhaité d’accès de l’abonnement, effectuer une tâche de test sur l’abonnement à l’aide d’un jeton d’application uniquement.

Pour obtenir un accès application seule jeton, suivez les instructions à partir de la section [accéder application seule jeton pour Azure AD Graph API](#app-azure-ad-graph), avec une valeur différente pour le paramètre de ressource : 

    https://management.core.windows.net/

La méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de l’exemple d’application ASP.NET MVC Obtient un accès application seule jeton pour le Gestionnaire de ressources Azure à l’aide de la bibliothèque d’authentification Active Directory pour .net.

#### <a name="get-applications-permissions-on-subscription"></a>Obtenir les autorisations de l’Application lors de l’abonnement

Pour vérifier que votre application a l’accès désiré sur un abonnement Azure, vous pouvez également appeler les [Autorisations de gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn906889.aspx) API. Cette approche est similaire à détermination si l’utilisateur dispose des droits de gestion de l’accès de l’abonnement. Toutefois, cette fois, appelez les autorisations API avec le jeton d’accès uniquement par l’application que vous avez reçu dans l’étape précédente.

La méthode [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) de l’application exemple ASP.NET MVC met en œuvre cet appel.

## <a name="manage-connected-subscriptions"></a>Gérer les abonnements connectés

Lorsque le rôle RBAC approprié est attribué au service de votre application principal de l’abonnement, votre application peut conserver surveillance/gestion à l’aide des jetons d’accès application uniquement pour le Gestionnaire de ressources Azure.

Si un propriétaire de l’abonnement Supprime l’attribution de rôle de votre application à l’aide du portail classique ou les outils de ligne de commande, votre application n’est plus en mesure d’accéder à cet abonnement. Dans ce cas, vous devez indiquer à l’utilisateur que la connexion avec l’abonnement a été interrompue à partir d’en dehors de l’application et leur donner une option pour « réparer » de la connexion. « Réparer » serait recréez simplement l’attribution de rôle qui a été supprimée en mode hors connexion.

Comme vous avez activé l’utilisateur se connecter abonnements à votre application, vous devez autoriser l’utilisateur à déconnecter trop abonnements. À partir d’access gestion point de vue, déconnectez consiste à effacer l’attribution de rôle comportant principal du service de l’application sur l’abonnement. Vous pouvez également un état dans l’application de l’abonnement peut-être également supprimé. Seuls les utilisateurs disposant des autorisations de gestion de l’accès de l’abonnement sont en mesure de vous déconnecter de l’abonnement.

La [méthode RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) de l’application exemple ASP.net MVC met en œuvre cet appel.

C’est tout : les utilisateurs peuvent désormais facilement vous connecter et gérer leurs abonnements Azure avec votre application.

