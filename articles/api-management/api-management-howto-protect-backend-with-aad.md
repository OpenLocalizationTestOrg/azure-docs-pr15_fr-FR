<properties
    pageTitle="Comment faire pour protéger un serveur principal API Web avec Azure Active Directory et la gestion de l’API"
    description="Découvrez comment protéger un serveur principal API Web avec Azure Active Directory et la gestion de l’API." 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Comment faire pour protéger un serveur principal API Web avec Azure Active Directory et la gestion de l’API

La vidéo suivante montre comment créer un serveur principal API Web et protéger à l’aide du protocole OAuth 2.0 avec Azure Active Directory et la gestion de l’API.  Cet article fournit une vue d’ensemble et des informations supplémentaires sur les étapes décrites dans la vidéo. Cette vidéo 24 minutes vous explique comment procéder pour :

-   Créer un serveur principal API Web et sécuriser avec AAD - commençant à 1:30
-   Importer l’API dans Gestion des API - commençant à 7:10
-   Configurer le portail développeur pour appeler l’API - commençant à 9:09
-   Configurer une application de bureau d’appeler l’API - commençant à 18:08
-   Configurer une stratégie de validation JWT pour autoriser préalablement demandes - commençant à 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Créer un répertoire Azure AD

Pour sécuriser votre API Web sauvegardée à l’aide d’Azure Active Directory vous devez disposer d’un un client AAD. Dans cette vidéo, un client nommé **APIMDemo** est utilisé. Pour créer un client AAD, se connecter au [Portail classique Azure](https://manage.windowsazure.com) , cliquez sur **Nouveau**->**Application Services**->**Active Directory**->**répertoire**->**Créer personnalisé**. 

![Azure Active Directory][api-management-create-aad-menu]

Dans cet exemple, un répertoire nommé **APIMDemo** est créé avec un domaine par défaut nommé **DemoAPIM.onmicrosoft.com**. Ce répertoire est utilisé dans l’ensemble de la vidéo.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Création d’un service Web API sécurisé par Azure Active Directory

Dans cette étape, un serveur principal API Web est créé à l’aide de Visual Studio 2013. Cette étape de la vidéo commence à 1:30. Pour créer l’API Web projet principal dans Visual Studio, cliquez sur **fichier**->**Nouveau**->**projet**et choisissez **Application Web ASP.NET** dans la liste de modèles **Web** . Dans cette vidéo, le projet se nomme **APIMAADDemo**. Cliquez sur **OK** pour créer le projet. 

![Visual Studio][api-management-new-web-app]

Cliquez sur **Web API** de **Sélectionner un modèle** pour créer un projet d’API Web. Pour configurer l’authentification Azure Directory, cliquez sur **Modifier l’authentification**.

![Nouveau projet][api-management-new-project]

Cliquez sur **Comptes d’organisation**et spécifiez le **domaine** de votre client AAD. Dans cet exemple, le domaine est **DemoAPIM.onmicrosoft.com**. Le domaine de votre annuaire peut être obtenu à partir de l’onglet **domaines** de l’annuaire.

![Domaines][api-management-aad-domains]

Configurer les paramètres de votre choix dans la boîte de dialogue **Modifier l’authentification** , puis cliquez sur **OK**.

![Modifier l’authentification][api-management-change-authentication]

Lorsque vous cliquez sur **OK** Visual Studio tentera d’enregistrer votre application avec votre annuaire Azure AD et vous pouvez être invité à se connecter à Visual Studio. Se connecter à l’aide d’un compte d’administrateur pour votre annuaire.

![Se connecter à Visual Studio][api-management-sign-in-vidual-studio]

Pour configurer ce projet comme une case à cocher de l’API Web Azure pour **hôte dans le cloud** , puis sur **OK**.

![Nouveau projet][api-management-new-project-cloud]

Vous pouvez être invité à se connecter à Azure, et vous pouvez ensuite configurer l’application Web.

![Configurer][api-management-configure-web-app]

Dans cet exemple, un nouveau **plan de services d’application** nommé **APIMAADDemo** est spécifié.

Cliquez sur **OK** pour configurer l’application Web et créer le projet.

## <a name="add-the-code-to-the-web-api-project"></a>Ajouter le code au projet API Web

L’étape suivante de la vidéo ajoute le code au projet API Web. Cette étape démarre à 4:35.

L’API Web dans cet exemple met en œuvre un service de calculatrice de base à l’aide d’un modèle et un contrôleur. Pour ajouter le modèle pour le service, droit **modèles** dans **L’Explorateur** , puis cliquez sur **Ajouter**, **classe**. Nommez la classe `CalcInput` et cliquez sur **Ajouter**.

Ajoutez le code suivant `using` instruction en haut de la `CalcInput.cs` fichier.

    using Newtonsoft.Json;

 Remplacez la classe générée par le code suivant.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Droit **contrôleurs** dans **L’Explorateur de solutions** , puis cliquez sur **Ajouter**->**contrôleur**. Choisissez **Contrôleur de 2 de l’API Web - vide** , puis cliquez sur **Ajouter**. Tapez **CalcController** pour le nom du contrôleur et cliquez sur **Ajouter**.

![Ajouter contrôleur][api-management-add-controller]

Ajoutez le code suivant `using` instruction en haut de la `CalcController.cs` fichier.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Remplacez la classe contrôleur généré par le code suivant. Ce code mettent en œuvre, la `Add`, `Subtract`, `Multiply`, et `Divide` opérations de l’API de calculatrice de base.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Appuyez sur **F6** pour générer et vérifier la solution.

## <a name="publish-the-project-to-azure"></a>Publiez le projet dans Azure

Dans cette étape Visual Studio projet est publié sur Azure. Cette étape de la vidéo commence à 5:45.

Pour publier le projet sur Azure, cliquez sur le projet **APIMAADDemo** dans Visual Studio et choisissez **Publier**. Conserver les paramètres par défaut dans la boîte de dialogue **Publier le site Web** et cliquez sur **Publier**.

![Publication sur le Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Accorder des autorisations pour l’application de service de version serveur Azure AD

Une nouvelle application pour le service principal est créée dans votre annuaire Azure AD dans le cadre du processus de configuration et publication de votre projet API Web. Dans cette étape de la vidéo, en commençant à 6:13, les autorisations sont accordées sur le serveur principal API Web.

![Application][api-management-aad-backend-app]

Cliquez sur le nom de l’application à configurer les autorisations requises. Accédez à l’onglet **configurer** , puis faites défiler jusqu'à la section **autorisations à d’autres applications** . Cliquez sur les **Autorisations des applications de** menu déroulant en regard de **Windows** **Azure Active Directory**, activez la case à cocher pour les **données de l’annuaire en lecture**, puis cliquez sur **Enregistrer**.

![Ajouter des autorisations][api-management-aad-add-permissions]

>[AZURE.NOTE] Si **Windows** **Azure Active Directory** n’est pas répertorié sous autorisations à d’autres applications, cliquez sur **Ajouter l’application** et l’ajouter dans la liste.

Prenez note de l' **Application Id URI** pour une utilisation dans une étape ultérieure lorsqu’une application Azure AD est configurée pour le portail de gestion de l’API de développeur.

![Id d’application URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importer le API Web Gestion des API

API est configurés à partir du portail de publisher API, qui est accessible via le portail classique Azure. Pour atteindre le portail publisher, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [Gérer votre première API][] .

![Portail de Publisher][api-management-management-console]

Les opérations peuvent être [ajoutés aux API manuellement](api-management-howto-add-operations.md)ou qu’ils puissent être importés. Dans cette vidéo, les opérations sont importées au format Swagger commençant à 6:40.

Créez un fichier nommé `calcapi.json` avec contenu suivant et enregistrez-le sur votre ordinateur. Assurez-vous que la `host` attribut points à la principale API Web. Dans cet exemple `"host": "apimaaddemo.azurewebsites.net"` est utilisé.

{« swagger » : « 2.0 », « info » : {« titre » : « Calculatrice », « description » : « Arithmétique sur HTTP ! », « version » : « 1.0 »}, « hôte » : « apimaaddemo.azurewebsites.net », « chemin » de base de : « / api », « modèles » : [« http »], « chemins d’accès » : { » / ajouter ? un = {a} & b = {b} » : {« obtenir » : {« description » : « Répond avec une somme de deux nombres. », « operationId » : « Ajouter deux entiers », « paramètres » : [{« nom » : « a », « in » : « requête », « description » : « premier opérande. Valeur par défaut est <code>51</code>. », « requis » : vrai, « par défaut » : « 51 », « énumération » : [« 51 »]}, {« nom » : « b », « in » : « requête », « description » : « Second opérande. Valeur par défaut est <code>49</code>. », « requis » : vrai, « par défaut » : « 49 », « énumération » : [« 49 »]}], « réponses » : {}}}, « / sub?a = {a} & b = {b} » : {« obtenir » : {« description » : « Répond avec une différence entre deux nombres. », « operationId » : « Soustraire deux entiers », « paramètres » : [{« nom » : « a », « in » : « requête », « description » : « premier opérande. Valeur par défaut est <code>100</code>. », « requis » : vrai, « par défaut » : « 100 », « énumération » : [« 100 »]}, {« nom » : « b », « in » : « requête », « description » : « Second opérande. Valeur par défaut est <code>50</code>. », « requis » : vrai, « par défaut » : « 50 », « énumération » : [« 50 »]}], « réponses » : {}}}, « / div?a = {a} & b = {b} » : {« obtenir » : {« description » : « Répond avec un quotient de deux nombres. », « operationId » : « Divise deux entiers », « paramètres » : [{« nom » : « a », « in » : « requête », « description » : « premier opérande. Valeur par défaut est <code>100</code>. », « requis » : vrai, « par défaut » : « 100 », « énumération » : [« 100 »]}, {« nom » : « b », « in » : « requête », « description » : « Second opérande. Valeur par défaut est <code>20</code>. », « requis » : vrai, « par défaut » : « 20 », « énumération » : [« 20 »]}], « réponses » : {}}}, « / mul?a = {a} & b = {b} » : {« obtenir » : {« description » : « Répond à un produit de deux nombres. », « operationId » : « Multiplier deux nombres entiers », « paramètres » : [{« nom » : « a », « in » : « requête », « description » : « premier opérande. Valeur par défaut est <code>20</code>. », « requis » : vrai, « par défaut » : « 20 », « énumération » : [« 20 »]}, {« nom » : « b », « in » : « requête », « description » : « Second opérande. Valeur par défaut est <code>5</code>. », « requis » : vrai, « par défaut » : « 5 », « énumération » : [« 5 »]}], « réponses » : {}}}}}

Pour importer la calculatrice API, cliquez sur **API** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Importer des API**.

![Bouton Importer API][api-management-import-api]

Effectuez les opérations suivantes pour configurer la calculatrice API.

1. Cliquez sur **à partir du fichier**, accédez à la `calculator.json` fichier que vous avez enregistré, puis cliquez sur le bouton d’option **Swagger** .
2. Tapez **calc** dans la zone de texte **suffixe dans l’URL de l’API Web** .
3. Cliquez dans la zone **produits (facultatifs)** et sélectionnez **Starter**.
4. Cliquez sur **Enregistrer** pour importer l’API.

![Ajouter la nouvelle API][api-management-import-new-api]

Une fois que l’API est importée, la page Résumé de l’API s’affiche dans le portail de publisher.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Appeler l’API sans succès à partir du portail pour les développeurs

À ce stade, l’API a été importé dans Management API, mais ne peut pas encore être appelé avec succès à partir du portail pour les développeurs, car le service en aval est protégé par l’authentification Azure Active Directory. Ceci est illustré dans la vidéo à partir d’en procédant comme suit 7:40.

Cliquez sur le **portail pour les développeurs** à partir d’en haut à droite du portail de publisher.

![Portail pour les développeurs][api-management-developer-portal-menu]

Cliquez sur **API** et cliquez sur la **Calculatrice** API.

![Portail pour les développeurs][api-management-dev-portal-apis]

Cliquez sur **essayer**.

![Essaie][api-management-dev-portal-try-it]

Cliquez sur **Envoyer** et notez l’état de la réponse de **401 non autorisé**.

![Envoyer][api-management-dev-portal-send-401]

La requête n’est pas autorisée, car le serveur principal API est protégé par Azure Active Directory. Avant d’appeler avec succès l’API le développeur portail doit être configuré pour autoriser les développeurs qui utilisent OAuth 2.0. Ce processus est décrit dans les sections suivantes.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Enregistrer le portail de développement comme une application AAD

La première étape de configuration du portail développeur pour autoriser les développeurs qui utilisent OAuth 2.0 consiste à enregistrer le portail de développement comme une application AAD. Ceci est illustré en commençant à 8:27 dans la vidéo.

Atteindre le client Azure AD à partir de la première étape de cette vidéo, dans cet exemple **APIMDemo** et accédez à l’onglet **Applications** .

![Nouvelle application][api-management-aad-new-application-devportal]

Cliquez sur le bouton **Ajouter** pour créer une nouvelle application Azure Active Directory, puis sélectionnez **Ajouter une application de développe de mon organisation**.

![Nouvelle application][api-management-new-aad-application-menu]

Cliquez sur **application Web et/ou API Web**, entrez un nom, puis cliquez sur la flèche suivant. Dans cet exemple **APIMDeveloperPortal** est utilisé.

![Nouvelle application][api-management-aad-new-application-devportal-1]

Pour **l’authentification URL** , entrez l’URL de votre service de gestion de l’API et ajout `/signin`. Dans cet exemple **https://contoso5.portal.azure-api.net/signin **est utilisé.

Pour **Application Id URL** Entrez l’URL de votre service de gestion de l’API et ajout de caractères uniques. Il peut s’agir tout caractère souhaité et dans cet exemple **https://contoso5.portal.azure-api.net/dp** est utilisé. Lorsque les **Propriétés d’application** sont configurés, activez la case à cocher pour créer l’application.

![Nouvelle application][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurer un serveur d’autorisation API Management OAuth 2.0

L’étape suivante consiste à configurer un serveur d’autorisation OAuth 2.0 Gestion des API. Cette étape est présentée dans la vidéo en commençant à 9:43.

Cliquez sur **sécurité** dans le menu de gestion de l’API gauche et cliquez sur **2.0 OAuth**, puis cliquez sur **Ajouter l’autorisation** server.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server]

Entrez un nom et une description facultative dans les champs **nom** et une **Description** . Ces champs sont utilisés pour identifier le serveur de l’autorisation OAuth 2.0 dans l’instance de service de gestion de l’API. **Démonstration de serveur d’autorisation** est utilisée dans cet exemple. Ultérieurement lorsque vous spécifiez un serveur 2.0 OAuth à utiliser pour l’authentification pour une API, vous allez sélectionner ce nom.

Pour l' **URL de la page d’inscription Client** , entrez une valeur d’espace réservé tel que `http://localhost`.  L' **URL de la page d’inscription Client** pointe vers la page à laquelle les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs de OAuth 2.0 qui prennent en charge de la gestion des utilisateurs de comptes. Dans cet exemple utilisateurs ne pas créer et configurer leurs propres comptes pour un espace réservé soit utilisé.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-1]

Ensuite, spécifiez les **URL de point de terminaison d’autorisation** et **jeton URL du point de terminaison**.

![Serveur d’autorisation][api-management-add-authorization-server-1a]

Ces valeurs peuvent être récupérées à partir de la page **Points de terminaison de l’application** de l’application AAD que vous avez créé pour le portail développeur. Pour accéder aux points de terminaison accédez à l’onglet **configuration** de l’application DAS et cliquez sur les **points de terminaison de vue**.

![Application][api-management-aad-devportal-application]

![Points de terminaison de vue][api-management-aad-view-endpoints]

Copiez **point de terminaison 2.0 OAuth d’autorisation** et collez-le dans la zone de texte **URL du point de terminaison d’autorisation** .

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-2]

Copiez **point de terminaison jeton OAuth 2.0** et collez-le dans la zone de texte **URL du point de terminaison jeton** .

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-2a]

Outre le coller dans le point de terminaison jeton, ajouter un paramètre de corps supplémentaire nommé **ressource** et l’utilisation de valeur l' **Application Id URI** à partir de l’application DAS pour le service de serveur principal qui a été créé quand le projet Visual Studio a été publié.

![Id d’application URI][api-management-aad-sso-uri]

Ensuite, spécifiez les informations d’identification du client. Voici les informations d’identification pour la ressource que vous voulez accéder, dans ce cas, le service principal.

![Informations d’identification client][api-management-client-credentials]

Pour obtenir l' **Id de Client**, accédez à l’onglet **configuration** de l’application DAS pour le service en aval et copiez l' **Id de Client**.

Pour obtenir le clic **Secret Client** **Sélectionnez durée** déroulante dans la section **clés** et spécifiez un intervalle. Dans cet exemple, 1 an est utilisé.

![ID de client][api-management-aad-client-id]

Cliquez sur **Enregistrer** pour enregistrer la configuration et afficher la clé. 

>[AZURE.IMPORTANT] Prenez note de cette touche. Une fois que vous fermez la fenêtre de configuration Azure Active Directory, la clé ne peut pas être affichée à nouveau.

Copiez la clé dans le Presse-papiers, revenez au portail publisher, collez la clé dans la zone de texte **Secret Client** , puis cliquez sur **Enregistrer**.

![Ajouter un serveur d’autorisation][api-management-add-authorization-server-3]

Immédiatement après les informations d’identification client est un code d’octroi. Copiez le code d’autorisation et revenez à votre application de portail développeur Azure AD configurer page et coller l’autorisation accorder dans le champ **URL de réponse** , puis cliquez à nouveau sur **Enregistrer** .

![URL de réponse][api-management-aad-reply-url]

L’étape suivante consiste à configurer les autorisations pour l’application de AAD du portail développeur. Cliquez sur **Autorisations de l’Application** , activez la case à cocher pour **lire les données de répertoire**. Cliquez sur **Enregistrer** pour enregistrer la modification, puis cliquez sur **Ajouter application**.

![Ajouter des autorisations][api-management-add-devportal-permissions]

Cliquez sur l’icône de recherche, tapez **APIM** dans la commençant par la boîte de dialogue, sélectionnez **APIMAADDemo**et cliquez sur la case à cocher Enregistrer.

![Ajouter des autorisations][api-management-aad-add-app-permissions]

Cliquez sur **Autorisations de délégué** pour **APIMAADDemo** et la case à cocher pour **APIMAADDemo Access**, puis cliquez sur **Enregistrer**. Cela permet du développeur de l’application portail pour accéder au service principal.

![Ajouter des autorisations][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Activer l’autorisation d’utilisateur de OAuth 2.0 pour l’API calculatrice

À présent que le serveur OAuth 2.0 est configuré, vous pouvez la spécifier dans les paramètres de sécurité pour votre API. Cette étape est présentée dans la vidéo en commençant à 14:30.

Cliquez sur **API** dans le menu de gauche, puis cliquez sur **Calculatrice** pour afficher et configurer ses paramètres.

![Calculatrice API][api-management-calc-api]

Accédez à l’onglet **sécurité** , activez la case à cocher **OAuth 2.0** , sélectionnez le serveur d’autorisation souhaité dans la liste déroulante **serveur d’autorisation** , puis cliquez sur **Enregistrer**.

![Calculatrice API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Appeler avec succès l’API calculatrice à partir du portail pour les développeurs

À présent que l’autorisation OAuth 2.0 est configurée sur l’API, ses opérations peuvent être appelées avec succès à partir du centre de développement. Cette étape est présentée dans la vidéo à partir de 15:00.

Naviguer vers l’opération **ajouter deux entiers** du service de calculatrice dans le portail développeur et cliquez sur **essayer**. Remarque le nouvel élément dans la section **autorisation** correspondant au serveur d’autorisation que vous venez d’ajouter.

![Calculatrice API][api-management-calc-authorization-server]

Sélectionnez **le code d’autorisation** dans la liste déroulante d’autorisations et entrez les informations d’identification du compte à utiliser. Si vous êtes déjà connecté avec le compte que vous ne pouvez pas être invité.

![Calculatrice API][api-management-devportal-authorization-code]

Cliquez sur **Envoyer** et notez l' **état de la réponse** de **200 OK** ainsi que les résultats de l’opération dans le contenu de la réponse.

![Calculatrice API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurer une application de bureau d’appeler l’API

La procédure suivante dans la vidéo démarre à 16 h 30 et configure une application de bureau simple d’appeler l’API. La première étape consiste à enregistrer l’application de bureau dans Azure AD et lui donner accès au répertoire et au service principal. 18:25 il inclut une démonstration de l’application de bureau une opération de la calculatrice API l’appel.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurer une stratégie de validation JWT pour autoriser préalablement demandes

La procédure finale dans la vidéo démarre 20:48 et vous montre comment utiliser la stratégie [JWT valider](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) pour autoriser préalablement demandes en validant les jetons d’accès de chaque demande entrante. Si la demande n’est pas validée par la stratégie JWT valider, la demande est bloquée par la gestion des API et n’est pas passée sur le serveur principal.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Pour une autre démonstration de la configuration et l’utilisation de cette stratégie, voir [Cloud garde épisode 177 : plus les fonctionnalités de gestion de l’API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) et avance rapide à 13:50. Avance rapide à 15:00 pour afficher les stratégies configurées dans l’éditeur de stratégie, puis à 18:50 pour une démonstration de l’appel à une opération à partir du portail développeur avec et sans le jeton d’autorisation requise.

## <a name="next-steps"></a>Étapes suivantes
-   Consultez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur la gestion des API.
-   D’autres moyens de sécuriser votre service serveur principal, voir [l’authentification par certificat commun](api-management-howto-mutual-certificates.md) et [se connecter via VPN ou ExpressRoute](api-management-howto-setup-vpn.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Gérer votre première API]: api-management-get-started.md
