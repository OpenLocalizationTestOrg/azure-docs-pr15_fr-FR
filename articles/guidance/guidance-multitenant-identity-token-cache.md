<properties
   pageTitle="Mise en cache des jetons d’accès dans une application partagée | Microsoft Azure"
   description="Mise en cache des jetons d’accès utilisées pour appeler un serveur principal API Web"
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


# <a name="caching-access-tokens-in-a-multitenant-application"></a>Mise en cache des jetons d’accès dans une application partagée

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série]. Vous trouverez également un [exemple d’application] complète qui accompagne cette série.

Il est relativement coûteux d’obtenir un accès OAuth jeton, car elle nécessite une requête HTTP au point de terminaison jeton. Par conséquent, il est judicieux de jetons cache dès que possible. La [Bibliothèque de d’authentification Azure AD] [ ADAL] (terme ADAL) met automatiquement en cache les jetons obtenus à partir d’Azure AD, y compris des jetons actualisation.

Terme ADAL fournit une implémentation de cache de jetons par défaut. Toutefois, du cache de jetons est destiné aux applications native client et est _pas_ adaptée aux applications web :

-   Il est une instance statique et pas thread-safe.
-   Elle n’adaptée à un grand nombre d’utilisateurs, étant donné que jetons de tous les utilisateurs coder le même dictionnaire.
-   Il ne peut pas être partagé entre les serveurs web dans une batterie de serveurs.

À la place, vous devez implémenter un cache jeton personnalisé qui est dérivée du terme ADAL `TokenCache` classe mais est adaptée à un environnement de serveur et fournit le niveau d’isolement entre les jetons pour différents utilisateurs.

La `TokenCache` classe stocke un dictionnaire de jetons, indexé par l’émetteur, de ressources, ID de client et utilisateur. Un cache jeton personnalisé doit écrire ce dictionnaire dans un magasin de sauvegarde, tel qu’un cache Redis.

Dans l’application Tailspin enquêtes, les `DistributedTokenCache` classe mettent en œuvre, le cache de jetons. Cette implémentation utilise [IDistributedCache] [ distributed-cache] abstraction de ASP.NET Core 1.0. De cette façon, n’importe quel `IDistributedCache` implémentation peut être utilisée comme magasin de sauvegarde.

-   Par défaut, l’application enquêtes utilise un cache Redis.
-   Pour un serveur web uniques, vous pouvez utiliser le ASP.NET Core 1.0 [en mémoire cache][in-memory-cache]. (Il s’agit également une option appropriée pour l’exécution de l’application localement pendant le développement.)

> [AZURE.NOTE] Le cache Redis n’est actuellement pas pris en charge pour .NET Core.

`DistributedTokenCache`stocke les données du cache sous forme de paires clé/valeur dans le magasin de sauvegarde. La clé est l’ID d’utilisateur plus ID client, afin que le magasin de sauvegarde conserve les données de cache distincte pour chaque combinaison d’utilisateur/client.

![Cache de jetons](media/guidance-multitenant-identity/token-cache.png)

Le magasin de sauvegarde est divisé par utilisateur. Pour chaque demande HTTP, les jetons pour cet utilisateur sont lues à partir du magasin de sauvegarde et chargés dans le `TokenCache` dictionnaire. Si Redis est utilisé comme magasin de stockage, chaque instance du serveur dans une batterie de serveurs lecture/écriture dans le cache même, et cette approche s’adapte à de nombreux utilisateurs.

## <a name="encrypting-cached-tokens"></a>Le chiffrement jetons mis en cache

Les jetons sont des données sensibles, car ils accorder l’accès aux ressources d’un utilisateur. (En outre, contrairement à un mot de passe, vous ne peut pas simplement stocker un hachage du jeton.) Par conséquent, il est essentiel de protéger les jetons d’être compromis. Le cache de secours Redis est protégé par un mot de passe, mais si quelqu'un obtient le mot de passe, qu’ils puissent accéder à tous les jetons d’accès mis en cache. Pour cette raison, la `DistributedTokenCache` chiffre tout ce qu’il écrit dans le magasin de sauvegarde. Le chiffrement est effectué à l’aide de la [protection des données] de ASP.NET Core 1.0[ data-protection] API.

> [AZURE.NOTE] Si vous déployez aux Sites Web de Azure, les clés de chiffrement sont sauvegardées sur le stockage en réseau et synchronisés sur tous les ordinateurs (voir [Gestion des clés][key-management]). Par défaut, les clés sont chiffrés pas lors de l’exécution dans des Sites Web Azure, mais vous pouvez [Activer le chiffrement à l’aide d’un certificat X.509][x509-cert-encryption].


## <a name="distributedtokencache-implementation"></a>Mise en œuvre DistributedTokenCache

La [DistributedTokenCache] [ DistributedTokenCache] classe est dérivée de la ADAL [TokenCache] [ tokencache-class] cours.

Dans le constructeur, la `DistributedTokenCache` classe crée une clé de l’utilisateur actuel et charge le cache de magasin de stockage :

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

La clé est créée en concaténant les ID d’utilisateur et l’ID de client. Ces deux éléments sont extraits de revendications figurant dans l’utilisateur `ClaimsPrincipal`:

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

Pour charger les données du cache, lisez le blob série à partir du magasin de sauvegarde, puis appeler `TokenCache.Deserialize` pour convertir le blob en cache les données.

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

Chaque fois que le terme ADAL accéder au cache, il se déclenche un `AfterAccess` événement. Si les cache des données a changé, la `HasStateChanged` propriété est vraie. Dans ce cas, mettre à jour le magasin de stockage pour refléter les modifications, puis définissez `HasStateChanged` sur false.

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache envoie deux autres événements :

- `BeforeWrite`. Appelé juste avant que terme ADAL écrit dans le cache. Vous pouvez l’utiliser pour mettre en œuvre une stratégie d’accès concurrentiels au
- `BeforeAccess`. Appelé juste avant que terme ADAL lit à partir du cache. Ici, vous pouvez recharger le cache pour obtenir la dernière version.

Dans notre exemple, nous avons décidé de ne pas gérer ces deux événements.

- Accès simultané, la dernière écriture wins. Qui est OK, car les jetons sont stockés séparément pour chaque utilisateur + client, pour un conflit se passe-t-il uniquement si le même utilisateur avait deux sessions de connexion simultanées.
- Pour la lecture, nous charger le cache à chaque requête. Les requêtes sont courte durée de vie. Si le cache est modifié pendant cette période, la requête suivante chercher la nouvelle valeur.

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article suivant de cette série : [sa fédération avec AD FS un client pour les applications partagées dans Azure][adfs]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[adfs]: guidance-multitenant-identity-adfs.md
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[partie d’une série]: guidance-multitenant-identity.md
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
