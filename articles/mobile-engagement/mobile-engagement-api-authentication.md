<properties 
    pageTitle="Vous authentifier avec Engagement Mobile API REST"
    description="Décrit comment faire pour vous authentifier avec l’API REST de Azure Mobile Engagement" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Vous authentifier avec Engagement Mobile API REST

## <a name="overview"></a>Vue d’ensemble

Ce document décrit comment obtenir un jeton AAD valide jeton pour vous authentifier avec l’API REST de Engagement Mobile. 

Il est supposé que vous avez un abonnement Azure valide et que vous avez créé une application Mobile Engagement en utilisant l’un de nos [Didacticiels pour les développeurs](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentification

Un Microsoft Azure Active Directory en fonction de jeton jeton est utilisé pour l’authentification. 

Ordre à la demande d’authentification une API, un en-tête d’autorisation doit être ajouté à chaque demande situé sous la forme suivante :

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Les jetons Azure Active Directory expirent dans 1 heure.

Il existe plusieurs méthodes pour obtenir un jeton. Dans la mesure où les API sont généralement appelées à partir d’un service cloud, vous souhaitez utiliser une clé d’API. Une clé de l’API dans la terminologie Azure s’appelle un mot de passe principal de Service. La procédure suivante décrit une façon de configurer manuellement.

### <a name="one-time-setup-using-script"></a>Programme d’installation unique (à l’aide de script)

Vous devez suivre le jeu d’instructions ci-dessous pour effectuer la configuration à l’aide d’un script PowerShell qui prend la durée minimale d’installation, mais utilise les paramètres par défaut plus autorisées. Si vous le souhaitez, vous pouvez également suivez les instructions de [configuration manuelle](mobile-engagement-api-authentication-manual.md) de le faire directement à partir du portail Azure et effectuer une configuration plus précie. 

1. Obtenir la dernière version d’Azure PowerShell [ici](http://aka.ms/webpi-azps). Pour plus d’informations sur les instructions de téléchargement, vous pouvez voir ce [lien](../powershell-install-configure.md).  

2. Une fois Azure PowerShell est installé, utilisez les commandes suivantes pour vous assurer d’avoir installé le **module Azure** :

    un. Vérifiez que le module Azure PowerShell est disponible dans la liste des modules disponibles. 
    
        Get-Module –ListAvailable 

    ![Modules Azure disponibles][1]
        
    b. Si vous ne trouvez pas le module Azure PowerShell dans la liste ci-dessus, vous devez exécuter ce qui suit :
        
        Import-Module Azure 
        
3. Connexion au Gestionnaire de ressources Azure à partir de PowerShell en exécutant la commande suivante et en fournissant votre nom d’utilisateur et mot de passe pour votre compte Azure : 
        
        Login-AzureRmAccount

4. Si vous avez plusieurs abonnements ensuite, vous devez exécuter ce qui suit :

    un. Obtenir une liste de tous vos abonnements et copiez le SubscriptionId de l’abonnement que vous voulez utiliser. Vérifiez que cet abonnement est la même que celle qui contient l’application d’Engagement Mobile dont vous souhaitez interagir avec l’aide des API. 

        Get-AzureRmSubscription

    b. Exécutez la commande suivante fournissant le SubscriptionId pour configurer l’abonnement à utiliser.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Copier le texte pour le script [AzureRmServicePrincipalOwner.ps1 de nouveau](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) sur votre ordinateur local et l’enregistrer comme une applet de commande PowerShell (par exemple, `APIAuth.ps1`) et exécutez-la `.\APIAuth.ps1`. 
    
6. Le script vous demandera permettent de fournir une entrée pour **au principalName**. Attribuez un nom approprié ici que vous souhaitez utiliser pour créer votre application Active Directory (par exemple, APIAuth). 

7. Une fois le script terminé, il affiche les éléments suivants quatre valeurs dont vous aurez besoin pour vous authentifier par programme avec AD Vérifiez que pour les copier. 
        
    **TenantId**, **SubscriptionId**, **ApplicationId**et **Secret**.

    Vous allez utiliser TenantId comme `{TENANT_ID}`, ApplicationId en tant que `{CLIENT_ID}` et secrète en tant que `{CLIENT_SECRET}`.

    > [AZURE.NOTE] Votre stratégie de sécurité par défaut peut-être vous empêcher d’exécuter un scripts PowerShell. Si c’est le cas, vous configurez temporairement votre stratégie d’exécution pour autoriser l’exécution de scripts à l’aide de la commande suivante :

        > Set-ExecutionPolicy RemoteSigned

8. Voici comment donnera l’ensemble des applets de commande PS. 

    ![][3]

9. Dans le portail de gestion Azure, vérifiez qu’une nouvelle application AD a été créée avec le nom que vous avez fourni pour le script appelé **au principalName** sous **Qu'afficher les Applications de mon entreprise est propriétaire**.

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Étapes à suivre pour obtenir un jeton valide

1. Appel de l’API avec les paramètres suivants et n’oubliez pas de remplacer le client\_ID, CLIENT\_ID et le CLIENT\_SECRET :

    - **URL de la requête** en tant que *https://login.microsoftonline.com/ {client\_ID} / oauth2 ne/jeton*
    - **En-tête de Type de contenu HTTP** comme *application/x-www-form-urlencoded*
    - **Corps de la demande HTTP** en tant que *accorder\_type = client\_informations d’identification et identifiant_client = {CLIENT\_ID} & client_secret = {CLIENT\_SECRET} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    Voici un exemple de demande :

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    Voici une réponse d’exemple :

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    Cet exemple inclus encodage des URL des paramètres de publication, `resource` valeur est réellement `https://management.core.windows.net/`. Veillez à URL également coder `{CLIENT_SECRET}` qu’il peut contenir des caractères spéciaux.

    > [AZURE.NOTE] Pour le test, vous pouvez utiliser un outil client HTTP comme [Fiddler](http://www.telerik.com/fiddler) ou [extension Postman de Chrome](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 

2. Dans chaque appel API, incluent désormais l’en-tête de demande d’autorisation :

        Authorization: Bearer {ACCESS_TOKEN}

    Si vous obtenez un code de 401 état renvoyé, vérifiez le corps de réponse, il peut vous indiquer le jeton a expiré. Dans ce cas, obtenir un nouveau jeton.

##<a name="using-the-apis"></a>À l’aide de l’API

Maintenant que vous avez un jeton valide, vous êtes prêt à effectuer les appels API.

1. Dans chaque demande d’API, vous devrez passer un jeton valide, non expiré que vous avez obtenu dans la section précédente.

2. Vous devrez plug-in certains paramètres dans la demande URI qui identifie votre application. La demande URI ressemble à ceci

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    Pour obtenir les paramètres, cliquez sur votre nom de l’application et cliquez sur tableau de bord et vous verrez une page comme celle-ci avec les 3 paramètres.

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** nom de votre groupe de ressources va agir en **MobileEngagement** , sauf si vous avez créé un nouvel identifiant. 

    ![Paramètres Mobile Engagement API URI][2]

>[AZURE.NOTE] <br/>
>1. Ignorer l’adresse racine API car il s’agissait de l’API précédentes.<br/>
>2. Si vous avez créé l’application à l’aide du portail Azure classique vous devez utiliser le nom de ressource d’Application qui est différent de celle du nom de l’Application. Si vous avez créé l’application dans le portail Azure vous devez utiliser le nom de l’application elle-même (il n’y a aucune distinction entre le nom de la ressource Application et nom de l’application pour les applications créées dans le nouveau portail).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



