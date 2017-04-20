<properties
   pageTitle="Pour protéger la confidentialité de l’application à l’aide de l’archivage sécurisé clé | Microsoft Azure"
   description="Comment un utiliser le service de l’archivage sécurisé clé pour stocker des données confidentielles application"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# <a name="using-azure-key-vault-to-protect-application-secrets"></a>À l’aide de l’archivage sécurisé de clé Azure pour protéger la confidentialité de l’application

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série]. Vous trouverez également un [exemple d’application] complète qui accompagne cette série.

## <a name="overview"></a>Vue d’ensemble

Il est courant d’avoir des paramètres de l’application qui sont sensibles et doivent être protégées, telles que :

- Chaînes de connexion de base de données
- Mots de passe
- Clés de chiffrement

Pour des raisons de sécurité, vous ne devez jamais stocker ces secrets dans le contrôle de code source. Il est trop facile de se perdre &mdash; même si votre référentiel de code source est privé. Et il n’est pas pratiquement suffisant secrets à partir de la public. Sur les projets volumineux, vous souhaiterez peut-être limiter les développeurs et les opérateurs peuvent accéder les secrets de fabrication. (Paramètres pour les environnements de test ou de développement sont différents).

Une option la plus sûre consiste à stocker ces secrets dans [L’archivage sécurisé de clé Azure][KeyVault]. Clé de l’archivage sécurisé est un service hébergé sur le nuage pour la gestion des clés de chiffrement et d’informations confidentielles. Cet article vous explique comment utiliser l’archivage sécurisé clé pour stocker des paramètres de configuration d’application vous.

Dans les [Enquêtes Tailspin] [ Surveys] application, les paramètres suivants sont secrètes :

- Chaîne de connexion de base de données.
- Chaîne de connexion Redis.
- Le code secret client pour l’application web.

Pour stocker les secrets de configuration dans l’archivage sécurisé clé, l’application enquêtes mettent en œuvre, un fournisseur de configuration personnalisé, crochets dans le [système de configuration]de ASP.NET Core 1.0[configuration]. Le fournisseur personnalisé lit les paramètres de configuration de l’archivage sécurisé clé lors du démarrage.

L’application enquêtes charge les paramètres de configuration à partir des emplacements suivants :

- Le fichier appsettings.json
- Le [stockent des données utilisateur confidentielles] [ user-secrets] (environnement de développement fins de test uniquement ;)
- L’environnement d’hébergement (paramètres de l’application dans les applications web Azure)
- Archivage sécurisé clé

Chacun de ces substitutions celui précédent, afin que les paramètres stockés dans l’archivage sécurisé clé ont la priorité.

> [AZURE.NOTE] Par défaut, le fournisseur de configuration de l’archivage sécurisé clé est désactivé. Il n’est pas nécessaire pour exécuter l’application localement. Vous devez l’activer dans un déploiement de production.

> Le fournisseur de l’archivage sécurisé clé est actuellement pas pris en charge pour .NET Core, car elle nécessite l' [Microsoft.Azure.KeyVault] [ Microsoft.Azure.KeyVault] package.

Au démarrage, l’application lit les paramètres de chaque fournisseur de configuration inscrite et les utilise pour remplir un objet options fortement typée. (Pour plus d’informations, voir [utilisation des Options et objets de configuration][options].)

## <a name="implementation"></a>Mise en œuvre

La [KeyVaultConfigurationProvider] [ KeyVaultConfigurationProvider] classe est un fournisseur de configuration qui se branche sur le [système de configuration]de ASP.NET Core 1.0[configuration].

Utiliser la `KeyVaultConfigurationProvider`, appelez le `AddKeyVaultSecrets` méthode d’extension de la classe de démarrage :

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Notez que `KeyVaultConfigurationProvider` requiert certains paramètres de configuration, qui doivent être stockés dans un des autres sources de configuration.

Lorsque l’application démarre, `KeyVaultConfigurationProvider` énumère toutes des codes secrets dans l’archivage sécurisé clé. Pour chaque code secret, il recherche une balise nommée « ConfigKey ». La valeur de l’indicateur est le nom du paramètre de configuration.

> [AZURE.NOTE] [Balises] [ key-tags] sont stockées avec une clé de métadonnées facultative. Balises sont utilisées ici parce que les noms de clés ne peut pas contenir de caractères deux-points ( :)).

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] Voir [KeyVaultConfigurationProvider.cs].

## <a name="setting-up-key-vault-in-the-surveys-app"></a>Configuration de la clé de l’archivage sécurisé dans l’application enquêtes

Conditions requises :

- Installer les [Applets de commande Gestionnaire de ressources Azure][azure-rm-cmdlets].
- Configurer l’application enquêtes comme décrit dans [l’exécution de l’application enquêtes][readme].

Principales étapes à suivre :

1. Configurer un utilisateur d’administration dans le client.
2. Configurer un certificat client.
3. Créer un archivage sécurisé clé.
4. Ajoutez les paramètres de configuration à votre l’archivage sécurisé clé.
5. Les commentaires du code qui permet l’archivage sécurisé clé.
6. Mettre à jour les secrets des utilisateurs de l’application.

### <a name="set-up-an-admin-user"></a>Configurer un utilisateur admin

> [AZURE.NOTE] Pour créer un archivage sécurisé clé, vous devez utiliser un compte qui peut gérer vos abonnements Azure. En outre, n’importe quelle application que vous autorisez à lire à partir de l’archivage sécurisé clé doit être enregistré dans un seul client que ce compte.

Dans cette étape, vous effectuerez assurer que vous pouvez créer un archivage sécurisé clé lors d’une connexion en tant qu’utilisateur à partir du client où l’application enquêtes est enregistrée.

Tout d’abord, modifiez le répertoire associé à votre abonnement Azure.

1. Se connecter au [portail de gestion Azure][azure-management-portal]

2. Cliquez sur **paramètres**.

    ![Paramètres](media/guidance-multitenant-identity/settings.png)

3. Sélectionnez votre abonnement Azure.

4. Cliquez sur **Modifier le répertoire** en bas du portail.

    ![Paramètres](media/guidance-multitenant-identity/edit-directory.png)

5. Dans « Modifier le répertoire associé », sélectionnez le client Azure AD où l’application enquêtes est enregistrée,

    ![Paramètres](media/guidance-multitenant-identity/edit-directory2.png)

6. Cliquez sur la flèche et compléter la boîte de dialogue.

Créez un utilisateur d’administration dans le client Azure AD où l’application enquêtes est enregistrée.

1. Connectez-vous au [portail de gestion Azure][azure-management-portal].

2. Sélectionnez le client Azure AD où votre application est enregistrée.

3. Cliquez sur **utilisateurs** > **Ajouter un utilisateur**.

4. Dans la boîte de dialogue **Ajouter un utilisateur** , attribuer l’utilisateur au rôle Administrateur général.

Ajoutez l’utilisateur admin comme administrateur de co-création pour votre abonnement Azure.

1. Connectez-vous au [portail de gestion Azure][azure-management-portal].

2. Cliquez sur **paramètres** et sélectionnez votre abonnement Azure.

3. Cliquez sur **administrateurs**

4. Cliquez sur **Ajouter** au bas du portail.

5. Entrez le message électronique de l’utilisateur d’administration que vous avez créé précédemment.

6. Activez la case à cocher pour l’abonnement.

7. Cliquez sur le bouton de coche pour compléter la boîte de dialogue.

![Ajouter un administrateur de co-création](media/guidance-multitenant-identity/co-admin.png)


### <a name="set-up-a-client-certificate"></a>Configurer un certificat client

1. Exécuter le script de PowerShell [/Scripts/Setup-KeyVault.ps1] [ Setup-KeyVault] comme suit :
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Pour le `Subject` paramètre, entrez un nom, tel que « surveysapp ». Le script génère un certificat auto-signé et qu’il stocke dans le magasin de certificats « utilisateur/personnel en cours ».

2. La sortie à partir du script est un fragment JSON. Ajoutez cette au manifeste d’application de l’application web, comme suit :

    1. Connectez-vous au [portail de gestion Azure] [ azure-management-portal] et accédez à votre répertoire Azure AD.

    2. Cliquez sur **Applications**.

    3. Sélectionnez l’application d’enquêtes.

    4.  Cliquez sur **Gérer le manifeste** et sélectionnez **Télécharger manifeste**.

    5.  Ouvrez le fichier JSON manifeste dans un éditeur de texte. Coller la sortie à partir du script dans le `keyCredentials` propriété. Il doit ressembler à ce qui suit :
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.  Enregistrer vos modifications dans le fichier JSON.

    7.  Revenez au portail. Cliquez sur **Gérer le manifeste** > **Télécharger manifeste** et télécharger le fichier JSON.

3. Ajouter le fragment JSON même au manifeste d’application du site web API (Surveys.WebAPI).

4. Exécutez la commande suivante pour obtenir l’empreinte numérique du certificat.
    ```
    certutil -store -user my [subject]
    ```
    où `[subject]` est la valeur que vous avez spécifiée pour objet le script PowerShell. L’empreinte numérique est répertorié sous « Certificat hachage (SHA1) ». Supprimer les espaces entre les nombres nombre hexadécimales.

Vous utiliserez l’empreinte numérique ultérieurement.

### <a name="create-a-key-vault"></a>Créer un archivage sécurisé clé

1. Exécuter le script de PowerShell [/Scripts/Setup-KeyVault.ps1] [ Setup-KeyVault] comme suit :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Lorsque vous êtes invité vos informations d’identification, connectez-vous en tant que l’utilisateur Azure AD que vous avez créé précédemment. Le script crée un nouveau groupe de ressources et un archivage sécurisé clé nouveau dans ce groupe de ressources.

    Remarque : pour le - paramètre emplacement, vous pouvez utiliser la commande PowerShell suivante pour obtenir une liste des régions valides :

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Réexécutez SetupKeyVault.ps, avec les paramètres suivants :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    où

    - nom de l’archivage sécurisé clés = le nom que vous avez attribué l’archivage sécurisé clé dans l’étape précédente.
    - Web application client ID = l’ID client pour l’application web enquêtes.
    - Web api client ID = l’ID client pour l’application Surveys.WebAPI.

    Exemple :
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] Vous pouvez obtenir le client ID à partir du [portail de gestion Azure][azure-management-portal]. Sélectionnez le client Azure AD, sélectionnez l’application, puis cliquez sur **configurer**.

    Ce script autorise l’application web et API web pour extraire les secrets de votre l’archivage sécurisé clé. Voir [prise en main l’archivage sécurisé de clé Azure] [ authorize-app] pour plus d’informations.

### <a name="add-configuration-settings-to-your-key-vault"></a>Ajouter des paramètres de configuration à votre l’archivage sécurisé clé

1. Exécutez SetupKeyVault.ps comme suit :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    où

    - nom de l’archivage sécurisé clés = le nom que vous avez attribué l’archivage sécurisé clé dans l’étape précédente.
    - Redis nom DNS = le nom DNS de votre instance de cache Redis.
    - Touche d’accès rapide de redis = touche d’accès rapide pour votre instance de cache Redis.

    Cette commande ajoute un code secret à votre l’archivage sécurisé clé. Le code secret est une paire nom/valeur plus une balise :

    -   Le nom de clé n’est pas utilisé par l’application, mais doit être unique dans l’archivage sécurisé clé.
    -   La valeur est la valeur de l’option de configuration, dans ce cas, la chaîne de connexion Redis.
    -   la balise « ConfigKey » conserve le nom de la clé de configuration.

2. À ce stade, il est recommandé de vérifier si vous avez enregistré avec succès les secrets de l’archivage sécurisé clé. Exécutez la commande PowerShell suivante :

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    La sortie doit indiquer la valeur secrète ainsi que des métadonnées :

    ![Sortie de PowerShell](media/guidance-multitenant-identity/get-secret.png)

3. Exécutez SetupKeyVault.ps à nouveau pour ajouter la chaîne de connexion de base de données :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    où `<<DB connection string>>` est la valeur de la chaîne de connexion de base de données.

    Pour le test avec la base de données locale, copiez la chaîne de connexion à partir du fichier Tailspin.Surveys.Web/appsettings.json. Si vous effectuez cette opération, veillez à modifier la barre oblique inverse double («\\\\') dans une barre oblique inverse. La barre oblique inverse double est un caractère d’échappement dans le fichier JSON.

    Exemple :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### <a name="uncomment-the-code-that-enables-key-vault"></a>Les commentaires du code qui permet l’archivage sécurisé clé

1. Ouvrez la solution Tailspin.Surveys.

2. Dans [Tailspin.Surveys.Web/Startup.cs][web-startup], recherchez le bloc de code suivantes et commentaire.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. Dans [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup], recherchez le bloc de code suivantes et commentaire.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. Dans [Tailspin.Surveys.Web/Startup.cs][web-startup], recherchez le code qui enregistre la `ICredentialService`. Supprimez les commentaires de la ligne qui utilise `CertificateCredentialService`et commentez la ligne qui utilise `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Cette modification permet à l’application web utiliser [Client assertion] [ client-assertion] pour obtenir des jetons d’accès OAuth. Avec assertion client, vous n’avez pas besoin code secret d’un client OAuth. Par ailleurs, vous pouvez stocker le code secret client dans l’archivage sécurisé clé. Toutefois, l’archivage sécurisé clé et assertion client qu'utilisent un client de certificats, si vous activez l’archivage sécurisé clé, il est recommandé d’activer assertion client également.

### <a name="update-the-user-secrets"></a>Mettre à jour les secrets utilisateur

Dans l’Explorateur, droit sur le projet Tailspin.Surveys.Web et sélectionnez **Gérer les Secrets utilisateur**. Dans le fichier secrets.json, supprimez le JSON existant et collez les éléments suivants :

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Remplacer les entrées [crochets] avec les valeurs correctes.

- `AzureAd:ClientId`: L’ID de client de l’application d’enquêtes.
- `AzureAd:WebApiResourceId`: Application ID URI que vous avez spécifié lorsque vous avez créé l’application Surveys.WebAPI dans Azure Active Directory.
- `Asymmetric:CertificateThumbprint`: L’empreinte numérique du certificat que vous avez précédemment, lorsque vous avez créé le certificat client.
- `KeyVault:Name`: Le nom de votre l’archivage sécurisé clé.

> [AZURE.NOTE] `Asymmetric:ValidationRequired`est faux car le certificat que vous avez créé précédemment n’était pas signé par une autorité de certification racine (CA). En production, utilisez un certificat est signé par une autorité de certification racine et définir `ValidationRequired` sur true.

Enregistrez le fichier secrets.json mis à jour.

Ensuite, dans l’Explorateur, droit sur le projet Tailspin.Surveys.WebApi et sélectionnez **Gérer les Secrets utilisateur**. Supprimez le JSON existant et collez les éléments suivants :

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Remplacer les entrées [crochets] et enregistrez le fichier secrets.json.

> [AZURE.NOTE] Pour le site web API, veillez à utiliser l’ID de client de l’application Surveys.WebAPI, pas l’application d’enquêtes.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[key-tags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[partie d’une série]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
