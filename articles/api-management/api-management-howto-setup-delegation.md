<properties 
    pageTitle="La délégation d’abonnement de l’enregistrement et produit utilisateur" 
    description="Découvrez comment déléguer l’abonnement à un tiers dans Azure API gestion utilisateur d’enregistrement et de produit." 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>La délégation d’abonnement de l’enregistrement et produit utilisateur

Délégation vous permet d’utiliser votre site Web existant pour la gestion des développeur signe-dans/connexion-haut et abonnement aux produits au lieu d’utiliser la fonctionnalité intégrée dans le portail développeur. Cela permet à votre site Web pour les propriétaires des données utilisateur et effectuer la validation de ces étapes d’une manière personnalisée.

## <a name="delegate-signin-up"> </a>Délégation développeur se connecter et d’abonnement

Déléguer développeur se connecter et d’abonnement à votre site Web existant, que vous devrez créer un point de terminaison délégation spécial sur votre site qui sert le point d’entrée pour cette demande initiée à partir du portail de gestion des API de développeur.

Le flux de travail final sera comme suit :

1. Développeur clique sur le lien de connexion ou d’abonnement en le portail de gestion de l’API de développeur
2. Navigateur est redirigé vers le point de terminaison de délégation
3. Point de terminaison de délégation en retour redirige vers ou présente l’interface utilisateur demandant à se connecter ou d’abonnement à un utilisateur
4. En cas de réussite, l’utilisateur est redirigé vers la page du portail développeur gestion des API de qu'ils démarrés à partir


Pour commencer, nous allons première configuration API gestion pour acheminer les demandes de via votre point de terminaison de délégation. Dans le portail de gestion de l’API de publisher, cliquez sur **sécurité** , puis sur l’onglet **délégation** . Cliquez sur la case à cocher pour activer 'Délégué se connecter et d’abonnement'.

![Page de délégation][api-management-delegation-signin-up]

* Déterminez ce que l’URL de votre point de terminaison délégation spécial sera et entrez cet élément dans le champ **URL du point de terminaison délégation** . 

* Dans le champ de **clé d’authentification délégation** Entrez un code secret qui sera utilisé pour calculer une signature fournie pour la vérification pour vous assurer que la demande arrive en effet de la gestion des API Azure. Vous pouvez cliquer sur le bouton **Générer** pour que l’API Managemnet aléatoirement générer une clé à votre place.

À présent, vous devez créer **point de terminaison de délégation**. Il doit effectuer un certain nombre d’actions :

1. Réception d’une demande sous la forme suivante :

    > *{URL de la page source} http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= & SEL = {chaîne} & sig = {chaîne}*

    Paramètres pour la connexion / d’abonnement à un cas de requête :
    - **opération**: identifie quel type de délégation demander celui-ci est, il peut uniquement être **connexion** dans ce cas
    - **returnUrl**: l’URL de la page où l’utilisateur a cliqué sur un lien de connexion ou d’abonnement
    - **sel**: une chaîne saline spéciale utilisée pour calculer un hachage de sécurité
    - **SIG**: un hachage sécurité calculé à utiliser pour votre propre comparaison hachage calculé

2. Vérifiez que la demande arrive à partir de gestion de l’API Azure (facultatif, mais vivement recommandé pour la sécurité)

    * Calculer un hachage HMAC SHA512 d’une chaîne selon les paramètres de requête **returnUrl** et **sel** ([exemple de code ci-dessous]) :
        > HMAC (**sel** + '\n' + **returnUrl**)
         
    * Comparer le hachage calculé au-dessus de la valeur du paramètre de requête **sig** . Si les deux valeurs de hachage correspondent, passez à l’étape suivante, dans le cas contraire refuser la demande.

2. Vérifiez que vous recevez une demande d’authentification-dans/se-up : le paramètre de requête **opération** aura la valeur «**connexion**».

3. Présenter à l’utilisateur avec une interface utilisateur pour se connecter ou d’abonnement

4. Si l’utilisateur est la signature à distance, vous devez créer un compte correspondant pour leur gestion des API. [Créer un utilisateur] avec l’API REST de gestion des API. Lorsque vous procédez ainsi, vous assurer que l’ID utilisateur à la même qu'il se trouve dans votre magasin d’utilisateurs ou à un numéro d’identification que vous pouvez effectuer le suivi de.

5. Lorsque l’utilisateur est authentifié avec succès :

    * [demander un jeton de (SSO) unique-authentification] via l’API REST de gestion des API

    * Ajouter un paramètre de requête returnUrl à l’URL de l’authentification unique que vous avez reçu de l’appel API ci-dessus :
        > par exemple, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * Redirigez l’utilisateur à l’URL de produits ci-dessus.

Outre l’opération de **connexion** , vous pouvez également effectuer la gestion des comptes en suivant les étapes précédentes et à l’aide d’une des opérations suivantes.

-   **ChangePassword**
-   **ChangeProfile**
-   **CloseAccount**

Vous devez passer les paramètres de requête suivants pour les opérations de gestion de compte.

-   **opération**: identifie le type de demande de délégation il est (ChangePassword, ChangeProfile ou CloseAccount)
-   **ID d’utilisateur**: l’id utilisateur du compte à gérer
-   **sel**: une chaîne saline spéciale utilisée pour calculer un hachage de sécurité
-   **SIG**: un hachage sécurité calculé à utiliser pour votre propre comparaison hachage calculé

## <a name="delegate-product-subscription"> </a>Délégation de l’abonnement de produit

Délégation de l’abonnement de produit fonctionne comme délégation de l’utilisateur se connecter/à distance. Le flux de travail final se présente comme suit :

1. Développeur sélectionne un produit dans le portail de gestion de l’API de développeur et clique sur le bouton s’abonner
2. Navigateur est redirigé vers le point de terminaison de délégation
3. Point de terminaison de délégation effectue produit requis abonnement étapes - cela est d’et peut entraîner la redirection vers une autre page pour demander des informations de facturation, poser des questions supplémentaires, ou simplement stocker les informations et ne pas nécessitant une action d’utilisateur


Pour activer la fonctionnalité, sur la page **délégation** cliquez sur **l’abonnement produit délégué**.

Vérifiez que le point de terminaison délégation effectue les actions suivantes :


1. Réception d’une demande sous la forme suivante :

    > *{opération} http://www.yourwebsite.com/apimdelegation?operation= & productId = {produit pour vous abonner à} & ID utilisateur = {utilisateur qui effectue demande} & SEL = {chaîne} & sig = {chaîne}*

    Paramètres de la requête pour le cas d’abonnement de produit :
    - **opération**: identifie le type de demande de délégation. Abonnement produit demandes les options valides sont :
        - « S’abonner » : une demande de s’abonner à un produit déterminé avec l’utilisateur fourni ID (voir ci-dessous)
        - « Annuler l’abonnement » : une demande de se désabonner d’un utilisateur à partir d’un produit
        - « Renouveler » : une demande de renouveler un abonnement (par exemple, qui peut-être être expirer)
    - **ID de produit**: l’ID du produit l’utilisateur a demandé se pour abonner à
    - **ID d’utilisateur**: l’ID de l’utilisateur pour lequel la demande est effectuée.
    - **sel**: une chaîne saline spéciale utilisée pour calculer un hachage de sécurité
    - **SIG**: un hachage sécurité calculé à utiliser pour votre propre comparaison hachage calculé


2. Vérifiez que la demande arrive à partir de gestion de l’API Azure (facultatif, mais vivement recommandé pour la sécurité)

    * Calculer un HMAC-SHA512 d’une chaîne selon les paramètres de requête **productId**, **nom d’utilisateur** et **salt** :
        > HMAC (**sel** + '\n' + **productId** + '\n' + **ID utilisateur**)
         
    * Comparer le hachage calculé au-dessus de la valeur du paramètre de requête **sig** . Si les deux valeurs de hachage correspondent, passez à l’étape suivante, dans le cas contraire refuser la demande.
    
3. Effectuer un traitement des abonnements produit en fonction du type d’opération demandée dans **opération** - par exemple, de facturation, autres questions, etc..

4. Sur l’abonnement avec succès l’utilisateur sur le produit de votre côté, s’abonner l’utilisateur pour le produit de gestion de l’API en [appelant l’API REST abonnement produit].

## <a name="delegate-example-code"></a> Exemple de Code ##

Ces exemples de code montrent comment tirer la *clé de validation de délégation*, qui est définie dans l’écran de délégation du portail publisher, pour créer un code HMAC qui est ensuite utilisé pour valider la signature, en prouvant qu’il la validité de la returnUrl passé. Le même code fonctionne pour les valeurs productId et ID utilisateur légères modifications.

**Code c# pour générer hachage de returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**Code NodeJS pour générer hachage de returnUrl**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la délégation, voir la vidéo suivante.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[demander un jeton de (SSO) unique-authentification]: http://go.microsoft.com/fwlink/?LinkId=507409
[créer un utilisateur]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[appel de l’API REST abonnement produit]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[exemple de code ci-dessous]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 