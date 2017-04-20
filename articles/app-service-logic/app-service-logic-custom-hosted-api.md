<properties
    pageTitle="Appeler une API personnalisée dans les applications de logique"
    description="À l’aide de votre API personnalisé hébergé sur Service d’application avec les applications de logique"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>À l’aide de votre API personnalisé hébergé sur Service d’application avec les applications de logique

Bien que les applications logique comporte un large éventail de plus de 40 connecteurs une grande variété de services, vous souhaiterez peut-être appeler votre propre API personnalisé que vous pouvez exécuter votre propre code. Une des façons plus simples et plus scalable pour héberger votre propre d’API web personnalisé consiste à utiliser l’application Service. Cet article explique comment participer à un site web API hébergé dans une application API de Service d’application, dans le navigateur ou une application mobile.

Pour plus d’informations sur la création d’API comme un déclencheur ou une action dans les applications de logique, consultez [cet article](app-service-logic-create-api-app.md).

## <a name="deploy-your-web-app"></a>Déployez votre application Web

Tout d’abord, vous devez déployer votre API comme une application Web dans le Service d’application. Les instructions fournies ici couvrent déploiement base : [créer une application web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).  Pendant que vous pouvez appeler n’importe quelle API à partir d’une application logique, pour une expérience optimale, nous vous recommandons ajouter des métadonnées Swagger d’intégrer facilement les actions de logique d’applications.  Vous pouvez trouver des informations sur [l’Ajout de swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Paramètres de l’API

Afin que le Concepteur d’applications logique analyser votre Swagger, il est important que vous activez CORS et définissez les propriétés APIDefinition de votre application web.  Cela est très facile à définir au sein du portail Azure.  Simplement ouvrir la carte de paramètres de votre application Web et sous la section API définir la « définition de l’API » à l’URL de votre fichier swagger.json (il s’agit généralement de https://{name}.azurewebsites.net/swagger/docs/v1) et ajouter une stratégie CORS pour ' *' pour autoriser les demandes à partir des applications logique Designer.

## <a name="calling-into-the-api"></a>Participent à l’API

Lorsque dans le portail d’applications logique, si vous avez défini CORS et les propriétés de définition de l’API doit pouvoir facilement ajouter des actions personnalisées API au sein de votre flux.  Dans le concepteur, vous pouvez sélectionner pour parcourir vos sites Web abonnement pour répertorier les sites Web avec une URL swagger définie.  Vous pouvez également utiliser le protocole HTTP + Swagger action pointez sur un swagger et actions disponibles et les entrées de la liste.  Enfin, vous pouvez toujours créer une requête à l’aide de l’action HTTP d’appeler n’importe quelle API, y compris ceux qui n’ont pas ou exposer un document swagger.

Si vous voulez protéger votre API, il existe deux façons différentes pour ce faire :

1. Aucun changement de code requis - Azure Active Directory ne peut servir à protéger votre API sans les modifications du code ou redéploiement.
1. Appliquer l’authentification de base, Auth AAD ou certificat Auth le code de votre API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Sécuriser les appels à votre API sans une modification du code

Dans cette section, vous devez créer deux applications Azure Active Directory – une pour votre application logique et l’autre pour votre application Web.  Vous devez vous authentifier les appels à votre application Web à l’aide de l’identité de service (id client et code secret) associée à l’application AAD pour votre application logique. Enfin, vous pouvez inclure l’application du code dans la définition de votre application logique.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Partie 1 : Configurer une identité de l’Application de votre application logique

Voici ce que l’application logique utilise pour authentification d’active directory. Vous uniquement *besoin* de le faire une fois pour votre annuaire. Par exemple, vous pouvez choisir d’utiliser la même identité pour toutes vos applications logique, bien que vous pouvez également créer des identités uniques par application logique si vous le souhaitez. Vous pouvez effectuer cette opération dans l’interface utilisateur ou utiliser PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Créer l’identité de l’application à l’aide du portail classique Azure

1. Accédez au [répertoire actif dans le portail Azure classique](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) , sélectionnez le répertoire que vous utilisez pour votre application Web
2. Cliquez sur l’onglet **Applications**
3. Cliquez sur **Ajouter** dans la barre de commandes en bas de la page
4. Donnez un nom à utiliser, cliquez sur la flèche suivant à votre identité
5. Placer dans une chaîne unique mis en forme comme un domaine dans deux zones de texte, puis cliquez sur la coche
6. Cliquez sur l’onglet **configurer** pour cette application
7. Copiez l' **ID de Client**, il sera utilisé dans votre application logique
8. Dans la section **touches** cliquez sur **Sélectionnez durée** et choisissez un an ou 2 ans
9. Cliquez sur le bouton **Enregistrer** dans la partie inférieure de l’écran (vous devrez peut-être patienter quelques secondes)
10. Veillez maintenant copier la clé dans la zone. Il vous sera également utilisé dans votre application logique

#### <a name="create-the-application-identity-using-powershell"></a>Créer l’identité de l’application à l’aide de PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Veillez à copier l' **ID de client**, l' **ID de l’Application** et le mot de passe que vous avez utilisé

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Partie 2 : Protéger votre application Web avec une identité de l’application AAD

Si votre application Web est déjà déployée vous pouvez l’activer simplement dans le portail. Dans le cas contraire, vous pouvez effectuer l’activation de l’autorisation fait partie de votre gestionnaire de déploiement de ressources Azure.

#### <a name="enable-authorization-in-the-azure-portal"></a>Activer l’autorisation dans le portail Azure

1. Accédez à l’application Web, puis cliquez sur les **paramètres** de la barre de commandes.
2. Cliquez sur **Authentification / d’autorisation**.
3. **Activer.**

À ce stade, une Application est créée automatiquement pour vous. Vous avez besoin Client ID de cette Application pour partie 3, donc vous devrez :

1. Accédez à [Active directory dans le portail classique Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) et sélectionnez votre annuaire.
2. Recherchez l’application dans la zone de recherche
3. Cliquez dessus dans la liste
4. Cliquez sur l’onglet **configurer**
5. Vous devriez voir l' **ID de Client**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Déploiement de votre application Web à l’aide d’un modèle de processeur

Tout d’abord, vous devez créer une application pour votre application Web. Il doit être différent de l’application qui est utilisée pour votre application logique. Commencez par suivre les étapes ci-dessus dans la partie 1, mais maintenant pour l’utilisation de la **page d’accueil** et **IdentifierUris** https:// réel**URL** de votre application Web.

>[AZURE.NOTE]Lorsque vous créez l’Application pour votre application Web, vous devez utiliser l' [approche portail classique Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), comme l’applet de commande PowerShell n’a pas défini les autorisations requises pour vous connecter les utilisateurs à un site Web.

Une fois que vous disposez du client ID et l’ID de client, inclure les éléments suivants en tant que sub ressource de l’application Web dans votre modèle de déploiement :

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Pour exécuter un déploiement automatiquement qui déploie un vide dans le navigateur et l’application de logique gagnante qui utilisent AAD, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Pour le modèle terminé, voir [appels d’application logique dans une API personnalisés hébergés sur application Service et protégés par AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>3e partie : Remplir la section d’autorisation dans l’application logique

Dans la section de **l’autorisation** de l’action **HTTP** :`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Élément | Description |
|---------|-------------|
| type | Type d’authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur est ActiveDirectoryOAuth. |
| client | L’identificateur de client permettant d’identifier le client AD. |
| audience | Obligatoire. La ressource que vous vous connectez à. |
| identifiant du client | L’identificateur de client de l’application Azure AD. |
| code secret | Obligatoire. Code secret du client qui demande le jeton. |

Le modèle ci-dessus possède déjà cette configuration, mais si vous créez l’application logique directement, vous devez inclure la section autorisation complète.

## <a name="securing-your-api-in-code"></a>Sécurisation de votre API dans le code

### <a name="certificate-auth"></a>Authentification de certificat

Vous pouvez utiliser les certificats clients pour valider les demandes entrantes à votre application Web. Voir [Comment à configurer commun l’authentification TLS pour le navigateur](../app-service-web/app-service-web-configure-tls-mutual-auth.md) pour savoir comment configurer votre code.

Dans la section *autorisation* vous devez fournir : `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Élément | Description |
|---------|-------------|
| type | Obligatoire. Type d’authentification. Pour les certificats SSL client, la valeur doit être ClientCertificate. |
| PFX | Obligatoire. Contenu codé en base 64 du fichier PFX. |
| mot de passe | Obligatoire. Mot de passe pour accéder au fichier PFX. |

### <a name="basic-auth"></a>Authentification de base

Vous pouvez utiliser l’authentification de base (par exemple, nom d’utilisateur et mot de passe) pour valider les demandes entrantes. Authentification de base est un motif courantes et vous pouvez le faire dans n’importe quelle langue que vous créez votre application dans.

Dans la section *autorisation* , vous devez fournir : `{"type": "basic","username": "test","password": "test"}`.

| Élément | Description |
|---------|-------------|
| type | Obligatoire. Type d’authentification. Pour l’authentification de base, la valeur doit être Basic. |
| nom d’utilisateur | Obligatoire. Nom d’utilisateur s’authentifier. |
| mot de passe | Obligatoire. Mot de passe pour l’authentification. |

### <a name="handle-aad-auth-in-code"></a>Poignée AAD auth dans le code

Par défaut, l’authentification Azure Active Directory que vous pouvez activer dans le portail ne fait pas une autorisation précise. Par exemple, il ne verrouille pas votre API pour un utilisateur spécifique ou une application, mais uniquement pour un client particulier.

Si vous voulez limiter l’API à simplement la logique application, par exemple, dans le code, vous pouvez extraire l’en-tête qui contient le JWT et vérifier qui l’appelant est, en refusant les demandes qui ne correspondent pas.

Aller plus loin, si vous voulez mettre en œuvre entièrement dans votre propre code et pas Tirez parti de la fonctionnalité de portail, vous pouvez lire cet article : [Utiliser Active Directory pour l’authentification dans le Service d’application Azure](../app-service-web/web-sites-authentication-authorization.md).

Vous avez besoin de suivre les étapes ci-dessus pour créer une identité de l’Application de votre application logique et l’utiliser pour appeler l’API.
