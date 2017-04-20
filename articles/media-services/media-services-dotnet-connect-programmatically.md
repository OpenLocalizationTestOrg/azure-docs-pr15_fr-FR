<properties 
    pageTitle="Connexion au compte de Services de support à l’aide de .NET" 
    description="Cette rubrique illustre comment se connecter aux Services de support en utilisant .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>Connexion au compte de Services de support à l’aide de Media Services SDK pour .NET

> [AZURE.SELECTOR]
- [RESTE](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


Cette rubrique explique comment obtenir une connexion à Microsoft Azure Media Services par programme lors de la programmation avec le Kit de développement de Services de support pour .NET.


## <a name="connecting-to-media-services"></a>Se connecter aux Services de support

Pour vous connecter aux Services de support par programme, vous devez avoir précédemment configuré un compte Azure, configuré Media Services sur ce compte et ensuite configurer un projet Visual Studio pour le développement avec le Kit de développement de Services de support pour .NET. Pour plus d’informations, voir le programme d’installation pour le développement avec le Kit de développement de Services de support pour .NET.

À la fin du processus de configuration de compte Media Services, vous avez obtenu les valeurs suivantes de la connexion requise. Les utiliser pour créer des connexions de programmation pour Media Services.

- Nom de votre compte Media Services.

- Votre clé de compte Media Services.

Pour rechercher les valeurs suivantes, accédez au portail de gestion Azure, sélectionnez votre compte de Service de support et cliquez sur l’icône «**Gérer les clés**» en bas de la fenêtre du portail. Cliquez sur l’icône en regard de chaque zone de texte copie la valeur dans le Presse-papiers système.


## <a name="creating-a-cloudmediacontext-instance"></a>Création d’une Instance CloudMediaContext

Pour commencer à programmer des Services de support vous devez créer une instance **CloudMediaContext** qui représente le contexte de serveur. La **CloudMediaContext** inclut les références aux collections importantes, y compris des travaux, immobilisations, fichiers, les stratégies d’accès et les repères.

>[AZURE.NOTE] La classe **CloudMediaContext** n’est pas thread safe. Vous devez créer un nouveau CloudMediaContext par thread ou par jeu d’opérations.


CloudMediaContext comporte cinq surcharges de constructeur. Il est recommandé d’utiliser des constructeurs qui prennent **MediaServicesCredentials** en tant que paramètre. Pour plus d’informations, voir la **Réutilisation les jetons Service d’accès contrôle** qui suit. 

L’exemple suivant utilise le constructeur CloudMediaContext(MediaServicesCredentials credentials) public :

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>Réutilisation jetons de Service de contrôle d’accès

Cette section montre comment réutiliser des jetons de Service de contrôle d’accès à l’aide de constructeurs CloudMediaContext acceptant MediaServicesCredentials en tant que paramètre.


[Contrôle d’accès Azure Active Directory](https://msdn.microsoft.com/library/hh147631.aspx) (également appelé Service de contrôle d’accès ou ACS) est un service en nuage qui offre un moyen facile d’authentification et d’autorisation des utilisateurs d’accéder à leurs applications web. Microsoft Azure Media Services contrôle l’accès à ses services via protocole OAuth qui requiert un jeton ACS. Media Services reçoit les jetons ACS à partir d’un serveur d’autorisation.

Lorsque vous développez avec le Kit de développement de Services de support, vous pouvez choisir de traiter les jetons ne pas, car le Kit de développement de code responsables de résoudre pour vous. Toutefois, laisser le Kit de développement entièrement gérer les jetons ACS permet d’accéder à des demandes de jetons inutiles. Demander des jetons prend du temps et consomme les ressources client et serveur. En outre, le serveur ACS limite les requêtes si le taux est trop élevé. La limite est de 30 requêtes par seconde, voir [Limitations du Service ACS](https://msdn.microsoft.com/library/gg185909.aspx) pour obtenir plus d’informations.

En commençant par la version 3.0.0.0 Media Services SDK, vous pouvez réutiliser les jetons ACS. Les constructeurs **CloudMediaContext** qui prennent **MediaServicesCredentials** en tant que paramètre Activer le partage les jetons ACS entre plusieurs contextes. La classe MediaServicesCredentials encapsule les informations d’identification Media Services. Si un jeton ACS est disponible et du délai d’expiration est connu, vous pouvez créer une nouvelle instance de MediaServicesCredentials avec le jeton et passer au constructeur de CloudMediaContext. Notez que le Kit de développement de Services de support sont automatiquement actualisées jetons chaque fois qu’ils arrivent à expiration. Il existe deux manières de réutiliser jetons ACS, comme indiqué dans les exemples ci-dessous.

- Vous pouvez mettre en cache l’objet **MediaServicesCredentials** en mémoire (par exemple, dans une variable de classe statique). Ensuite, passez l’objet mis en cache au constructeur CloudMediaContext. L’objet MediaServicesCredentials contient un jeton ACS qui peut être réutilisé s’il est toujours valide. Si le jeton n’est pas valide, il sera actualisée par Media Services SDK à l’aide d’identification fournies au constructeur MediaServicesCredentials.

    Notez que l’objet **MediaServicesCredentials** Obtient un jeton valide après que la RefreshToken est appelée. La **CloudMediaContext** appelle la méthode **RefreshToken** dans le constructeur. Si vous prévoyez d’enregistrer les valeurs de jeton dans un espace de stockage externe, veillez à vérifier si la valeur TokenExpiration est valide avant d’enregistrer les données jetons. S’il n’est pas valide, appelez RefreshToken avant la mise en cache.

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Vous pouvez également mettre en cache la chaîne AccessToken et les valeurs de TokenExpiration. Les valeurs ultérieurement peuvent servir à créer un nouvel objet MediaServicesCredentials avec les données jetons mis en cache.  Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou ordinateurs.

    Extraits de code suivants appeler les méthodes SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage et UpdateTokenDataInExternalStorageIfNeeded qui ne sont pas définies dans cet exemple. Vous pouvez définir ces méthodes pour stocker, extraire et mettre à jour des données jetons dans un espace de stockage externes. 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    Les valeurs de jeton enregistrés permet de créer MediaServicesCredentials.


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Mettre à jour la copie jeton au cas où le jeton a été mis à jour par Media Services SDK. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- Si vous avez plusieurs comptes Media Services (par exemple, pour charger le partage à des fins ou Geo Student) vous pouvez mettre en cache objets MediaServicesCredentials à l’aide de la collection System.Collections.Concurrent.ConcurrentDictionary (la collection ConcurrentDictionary représente une collection thread-safe des paires clé/valeur qui sont accessibles par plusieurs threads simultanément). Vous pouvez ensuite utiliser la méthode GetOrAdd pour obtenir les informations d’identification mises en cache. 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>Connexion à un compte Media Services situé dans l’angle en Chine du Nord

Si votre compte se trouve dans la région en Chine du Nord, utilisez le constructeur suivant :

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Par exemple :


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>Stockage de valeurs de connexion dans la Configuration

Il est vivement recommandé pour stocker les valeurs de connexion, notamment les valeurs sensibles telles que votre nom de compte et le mot de passe, dans la configuration. En outre, il est recommandé pour chiffrer les données de configuration sensibles. Vous pouvez chiffrer le fichier de configuration à l’aide du Windows système (ENCRYPTING). Pour l’activer sur un fichier, cliquez sur le fichier, sélectionnez **Propriétés**et activer le chiffrement sous l’onglet Paramètres **avancés** . Ou vous pouvez créer une solution personnalisée pour chiffrer les parties sélectionnées d’un fichier de configuration à l’aide de configuration protégée. Voir [chiffrement Configuration informations à l’aide de Configuration protégée](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Le fichier App.config suivant contient les valeurs de connexion requise. Les valeurs de la <appSettings> élément correspondent aux valeurs requises que vous avez obtenu à partir du processus de configuration de compte Media Services.

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


Pour extraire les valeurs de connexion de configuration, vous pouvez utiliser la classe **ConfigurationManager** et puis assigner les valeurs aux champs dans votre code :
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
