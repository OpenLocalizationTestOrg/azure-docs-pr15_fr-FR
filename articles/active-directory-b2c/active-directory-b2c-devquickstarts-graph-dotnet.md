<properties
    pageTitle="B2C Azure Active Directory : Utiliser le graphique de l’API | Microsoft Azure"
    description="L’appel de l’API Graph pour un client B2C à l’aide d’une identité de l’application pour automatiser le processus."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD B2C : Utiliser le graphique de l’API

Azure Active Directory (AD Azure) B2C clients sont souvent très volumineux. Cela signifie que nombreuses tâches de gestion courantes de client doivent être effectuées par programme. Gestion des utilisateurs est un exemple principal. Vous devrez migrer un magasin utilisateur existant vers un client B2C. Vous souhaiterez peut-être héberger d’inscription des utilisateurs dans votre propre page et créer des comptes d’utilisateur dans Active Directory Azure en coulisses. Ces types de tâches nécessitent la possibilité de créer, lire, mettre à jour et supprimer des comptes d’utilisateur. Vous pouvez effectuer les tâches à l’aide de l’API Azure AD Graph.

Pour les clients B2C, il existe deux modes de communication avec l’API de graphique.

- Pour les tâches interactives, exécution unique, vous devez ou la définir comme un compte d’administrateur dans le client B2C lorsque vous effectuez les tâches. Ce mode, un administrateur doit connectez-vous à l’aide des informations d’identification avant qu’administrateur peut effectuer des appels à l’API de graphique.
- Pour les tâches automatisés en continu, vous devez utiliser un type de compte de service que vous fournissez avec les privilèges nécessaires pour effectuer des tâches de gestion. Dans Azure Active Directory, vous pouvez effectuer cette opération en enregistrement d’une application couple à Azure Active Directory. Pour cela, à l’aide d’un **ID de l’Application** qui utilise les [informations d’identification du client OAuth 2.0 accorder](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Dans ce cas, l’application joue lui-même, pas en tant qu’utilisateur, d’appeler l’API Graph.

Dans cet article, nous verrons comment effectuer le cas d’utilisation automatique. Pour montrer, nous allons créer une .NET 4.5 `B2CGraphClient` qui effectue utilisateur créer, lire, mettre à jour et supprimer des opérations. Le client aura une interface de ligne de Windows (commande) qui vous permet d’appeler différentes méthodes. Toutefois, le code est écrit de se comporter de façon automatisée, non interactive.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtenir un client Azure AD B2C

Avant de pouvoir créer des applications ou des utilisateurs, ou interagir avec Azure AD tout, vous avez besoin d’un client Azure AD B2C et un compte d’administrateur global dans le client. Si vous n’avez pas déjà fait, un client [prise en main Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Enregistrer une application de service de votre client

Une fois que vous avez un client B2C, vous devez créer votre application de service à l’aide des applets de commande Azure AD PowerShell.
Tout d’abord, téléchargez et installez [l’Assistant Microsoft Online Services se connecter](http://go.microsoft.com/fwlink/?LinkID=286152). Puis téléchargez et installez le [module Azure Active Directory de 64 bits de Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.IMPORTANT]
Pour utiliser l’API de graphique à votre client B2C, vous devrez enregistrer une application dédiée à l’aide de PowerShell. Suivez les instructions de cet article pour ce faire. Vous ne pouvez pas réutiliser les applications B2C existants que vous avez enregistré dans le portail Azure.

Après avoir installé le module PowerShell, ouvrez PowerShell et se connecter à votre client B2C. Après avoir exécuté `Get-Credential`, vous serez invité pour un nom d’utilisateur et mot de passe, entrez le nom d’utilisateur et mot de passe de votre compte d’administrateur client B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Avant de créer votre application, vous devez générer un nouveau **secret client**.  Votre application utilisera le code secret client s’authentifier à Azure Active Directory et d’acquérir des jetons d’accès. Vous pouvez générer un code secret valide de PowerShell :

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

La commande finale doit imprimer votre nouveau code secret client. Copier dans un emplacement sûr. Vous en aurez besoin ultérieurement. Vous pouvez maintenant créer votre application en fournissant le nouveau code secret client comme informations d’identification pour l’application :

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Si vous créez avec succès l’application, elle doit imprimer les propriétés de l’application comme ceux présentés ci-dessus. Vous devrez fournir les `ObjectId` et `AppPrincipalId`, par conséquent, trop copier ces valeurs.

Après avoir créé une application de votre client B2C, vous devez affecter les autorisations que requises pour effectuer les opérations de l’utilisateur. Affecter les trois rôles d’application : lecteurs d’annuaire (pour lire les utilisateurs), les auteurs de répertoire (pour créer et mettre à jour les utilisateurs) et un administrateur de compte d’utilisateur (pour supprimer des utilisateurs). Ces rôles ont connu identificateurs, vous pouvez remplacer le `-RoleMemberObjectId` paramètre avec `ObjectId` à partir du haut et exécutez les commandes suivantes. Pour afficher la liste de tous les rôles d’annuaire, essayez d’exécuter `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Vous disposez d’une application qui dispose des autorisations nécessaires pour créer, lire, mettre à jour et supprimez des utilisateurs de votre client B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Télécharger, configurer et créer l’exemple de code

Tout d’abord, téléchargez l’exemple de code et découvrez qu’il fonctionne. Nous prendra puis observer de celle-ci.  Vous pouvez [Télécharger l’exemple de code sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Vous pouvez également le cloner dans un répertoire de votre choix :

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Ouvrir le `B2CGraphClient\B2CGraphClient.sln` solution Visual Studio dans Visual Studio. Dans la `B2CGraphClient` du projet, ouvrez le fichier `App.config`. Remplacer les paramètres de trois application par vos propres valeurs :

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, avec le bouton droit sur le `B2CGraphClient` solution et reconstruction de l’échantillon. Si vous parvenez, vous disposez à présent une `B2C.exe` fichier exécutable situé dans `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Créer des opérations utilisateur à l’aide de l’API de graphique

Pour utiliser le B2CGraphClient, ouvrez une `cmd` de commande Windows invite et modifier votre annuaire à la `Debug` répertoire. Réexécutez le `B2C Help` commande.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Ceci affiche une brève description de chaque commande. Chaque fois que vous appelez une de ces commandes, `B2CGraphClient` effectue une demande à l’API Azure AD Graph.

### <a name="get-an-access-token"></a>Obtenir un jeton d’accès

Toute demande à l’API Graph requiert un jeton d’accès pour l’authentification. `B2CGraphClient`utilise l’open source Active Directory authentification bibliothèque (terme ADAL) pour aider à acquérir des jetons d’accès. Terme ADAL facilite l’acquisition jeton en fournissant une API simple et en prenant soin de certaines informations importantes, telles que la mise en cache des jetons d’accès. Vous n’êtes pas obligé de terme ADAL permet d’obtenir des jetons, cependant. Vous pouvez également obtenir des jetons en créant des requêtes HTTP.

> [AZURE.NOTE]
    Cet exemple utilise ADAL v2 afin de communiquer avec l’API de graphique.  Vous devez utiliser ADAL v2 ou v3 afin d’obtenir les jetons d’accès qui peuvent être utilisés avec l’API Azure AD Graph.

Lorsque `B2CGraphClient` s’exécute, il crée une instance de le `B2CGraphClient` cours. Le constructeur pour cette classe configure une structure d’authentification ADAL :

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Nous allons utiliser la `B2C Get-User` commande par exemple. Lorsque `B2C Get-User` est appelé sans les entrées supplémentaires, les appels infrastructure du langage commun le `B2CGraphClient.GetAllUsers(...)` méthode. Cette méthode appelle `B2CGraphClient.SendGraphGetRequest(...)`, qui envoie une demande HTTP GET à l’API de graphique. Avant de `B2CGraphClient.SendGraphGetRequest(...)` envoie GET demande, il obtient d’abord un accès jeton à l’aide du terme ADAL :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Vous pouvez obtenir un accès à un jeton pour l’API Graph en appelant le terme ADAL `AuthenticationContext.AcquireToken(...)` méthode. Terme ADAL, retourne un `access_token` qui représente l’identité de l’application.

### <a name="read-users"></a>Lire les utilisateurs

Lorsque vous souhaitez obtenir une liste des utilisateurs ou obtenir un utilisateur particulier à partir de l’API de graphique, vous pouvez envoyer un HTTP `GET` demander à la `/users` point de terminaison. Une demande de tous les utilisateurs dans un client ressemble à ceci :

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour afficher cette demande, exécutez :

 ```
 > B2C Get-User
 ```

Il existe deux points importants à signaler :

- Le jeton d’accès acquis via le terme ADAL est ajouté à la `Authorization` en-tête à l’aide de la `Bearer` jeu.
- Pour les clients B2C, vous devez utiliser le paramètre de requête `api-version=1.6`.

Les deux de ces informations sont gérées dans le `B2CGraphClient.SendGraphGetRequest(...)` méthode :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Créer des comptes d’utilisateur consommateur

Lorsque vous créez des comptes d’utilisateurs dans votre client B2C, vous pouvez envoyer un HTTP `POST` demander à la `/users` point de terminaison :

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

La plupart de ces propriétés dans cette demande est requis pour créer des utilisateurs grand public. Pour plus d’informations, cliquez [ici](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Notez que la `//` commentaires ont été incluses pour illustration. N’incluez pas les dans une demande de réelle.

Pour afficher la demande, exécutez une des commandes suivantes :

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

La `Create-User` commande prend un fichier .json comme paramètre d’entrée. Cet onglet contient une représentation JSON d’un objet utilisateur. Il existe deux exemples de fichiers .json dans l’exemple de code : `usertemplate-email.json` et `usertemplate-username.json`. Vous pouvez modifier ces fichiers selon vos besoins. Outre les champs obligatoires ci-dessus, plusieurs champs facultatifs que vous pouvez utiliser sont inclus dans ces fichiers. Vous trouverez plus d’informations sur les champs facultatifs dans la [référence d’entité API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Vous pouvez voir comment la demande POST est construite dans `B2CGraphClient.SendGraphPostRequest(...)`.

- Il connecte un jeton d’accès à la `Authorization` en-tête de la demande.
- Il définit `api-version=1.6`.
- Il inclut l’objet utilisateur JSON dans le corps de la demande.

> [AZURE.NOTE]
Si les comptes que vous voulez migrer à partir d’un magasin utilisateur existant a force de mot de passe inférieur à la [rupture de mot de passe fort mises en œuvre par Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), vous pouvez désactiver l’exigence de mot de passe fort à l’aide de la `DisableStrongPassword` valeurs contenues dans les `passwordPolicies` propriété. Par exemple, vous pouvez modifier la demande de l’utilisateur créer fournie ci-dessus comme suit : `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Mettre à jour les comptes d’utilisateurs grand public

Lorsque vous mettez à jour les objets utilisateur, le processus est similaire à celui que vous utilisez pour créer des objets utilisateur. Mais ce processus utilise le protocole HTTP `PATCH` méthode :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

Essayez de mettre à jour un utilisateur en mettant à jour vos fichiers JSON avec de nouvelles données. Vous pouvez ensuite utiliser `B2CGraphClient` pour exécuter une des commandes suivantes :

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecter la `B2CGraphClient.SendGraphPatchRequest(...)` méthode pour plus d’informations sur l’envoi de cette demande.

### <a name="search-users"></a>Rechercher des utilisateurs

Vous pouvez rechercher des utilisateurs dans votre client B2C dans de deux façons. Un, à l’aide de l’utilisateur d’objet ID ou les deux, à l’aide se connecter identificateur de l’utilisateur (c'est-à-dire, la `signInNames` propriété).

Exécutez une des commandes suivantes pour rechercher un utilisateur spécifique :

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Voici quelques exemples :

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Supprimer des utilisateurs

Le processus de suppression d’un utilisateur est simple. Utiliser le protocole HTTP `DELETE` méthode et construction l’URL avec la bonne ID d’objet :

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour visualiser un exemple, entrez la commande suivante et afficher la demande de suppression est imprimée dans la console :

```
> B2C Delete-User <object-id-of-user>
```

Inspecter la `B2CGraphClient.SendGraphDeleteRequest(...)` méthode pour plus d’informations sur l’envoi de cette demande.

Vous pouvez effectuer de nombreuses autres actions avec l’API Azure AD Graph en plus de gestion des utilisateurs. La [référence de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fournit des détails sur chaque action, ainsi que des demandes d’exemples.

## <a name="use-custom-attributes"></a>Utiliser des attributs personnalisés

La plupart des applications consommateur nécessaire pour stocker un type d’informations de profil utilisateur personnalisées. Première vous pouvez le faire consiste à définir un attribut personnalisé dans votre client B2C. Vous pouvez ensuite traiter cet attribut la même façon que vous considérez toute autre propriété sur un objet utilisateur. Vous pouvez mettre à jour l’attribut, supprimez l’attribut, par l’attribut de requête, envoyer l’attribut sous forme d’une demande de connexion à jetons et bien plus encore.

Pour définir un attribut personnalisé dans votre client B2C, voir la [référence de l’attribut personnalisé B2C](active-directory-b2c-reference-custom-attr.md).

Vous pouvez afficher les attributs personnalisés définis à l’aide de votre client B2C `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Le résultat de ces fonctions pour afficher les détails de chaque attribut personnalisé, tel que :

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Vous pouvez utiliser le nom complet, tel que `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, en tant que propriété sur vos objets utilisateur.  Mettre à jour votre fichier .json avec la nouvelle propriété et une valeur pour la propriété, puis exécutez :

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

À l’aide de `B2CGraphClient`, vous disposez d’une application de service qui peut gérer vos utilisateurs client B2C par programme. `B2CGraphClient`utilise sa propre identité de l’application s’authentifier à l’API Azure AD Graph. Il pose également jetons à l’aide d’un code secret client. Lors de l’incorporation de cette fonctionnalité dans votre application, n’oubliez pas quelques points clés pour applications B2C :

- Vous devez accorder à l’application les autorisations appropriées dans le client.
- Pour l’instant, vous devez v2 ADAL permet d’obtenir les jetons d’accès. (Vous pouvez également envoyer des messages du protocole directement, sans l’aide d’une bibliothèque.)
- Lorsque vous appelez l’API de graphique, utilisez `api-version=1.6`.
- Lorsque vous créez et mettez à jour les utilisateurs grand public, certaines propriétés sont requises, comme décrit ci-dessus.

Si vous avez des questions ou les demandes d’action que vous voulez effectuer à l’aide de l’API Graph sur votre client B2C, laisser un commentaire sur cet article ou un problème de fichier dans le référentiel GitHub code échantillon.
