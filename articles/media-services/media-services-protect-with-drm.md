<properties
    pageTitle="L’utilisation du chiffrement courantes dynamique PlayReady et/ou Widevine | Microsoft Azure"
    description="Microsoft Azure Media Services vous permet d’effectuer des flux MPEG-tiret, Smooth Streaming et diffusion Live Http (TLS) protégés à l’aide Microsoft PlayReady DRM. Il vous permet également de remise tiret chiffrée avec Widevine DRM. Cette rubrique indique comment chiffrer dynamiquement avec PlayReady et Widevine DRM."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>L’utilisation du chiffrement courantes dynamique PlayReady et/ou Widevine

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Microsoft Azure Media Services vous permet d’effectuer des flux MPEG-tiret, Smooth Streaming et diffusion Live HTTP (TLS) protégés à l’aide [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). Il vous permet également d’effectuer des flux tiret chiffrés avec Widevine DRM des licences. PlayReady et Widevine sont chiffrés par la spécification de chiffrement courantes (ISO/IEC 23001-7 CENC). Vous pouvez utiliser [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (en commençant par la version 3.5.1) ou API REST pour configurer votre AssetDeliveryConfiguration pour utiliser Widevine.

Media Services fournit un service de proposer PlayReady et Widevine DRM des licences. Media Services fournit également des API qui vous permettre de configurer les droits et restrictions que vous souhaitez pour le runtime PlayReady ou Widevine DRM appliquer lorsqu’un utilisateur lit un contenu protégé. Lorsqu’un utilisateur demande un contenu protégé par DRM, l’application lecteur demandera une licence à partir du service de licence AMS. Le service de licence AMS émet une licence au lecteur s’il est autorisé. Une licence PlayReady ou Widevine contient la clé de déchiffrement qui peut être utilisée par le lecteur client à déchiffrer et le contenu du flux.


Vous pouvez également utiliser les partenaires AMS suivants pour vous aider à effectuer des licences Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Pour plus d’informations, voir : intégration à [Axinom](media-services-axinom-integration.md) et [castLabs](media-services-castlabs-integration.md).

Media Services prend en charge plusieurs méthodes permettant d’autoriser les utilisateurs qui font principales demandes. La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou restriction des jetons. La stratégie restreint jeton doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de [Jetons Web Simple](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) et [JSON Web jeton](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Pour plus d’informations, voir Configurer la stratégie d’autorisation de la clé de contenu.

Pour tirer parti de chiffrement dynamiques, vous devez disposer d’un bien qui contient un jeu de fichiers MP4 multi-débit ou multi-débit Smooth Streaming des fichiers source. Vous devez également configurer les stratégies de remise pour la ressource (décrites plus loin dans cette rubrique). Puis, selon le format spécifié dans l’URL de diffusion en continu, le serveur de diffusion en continu à la demande garantit que le flux est remis dans le protocole que vous avez choisi. Par conséquent, vous ne devez stocker et payer pour les fichiers dans un format de stockage unique et Media Services génération et répondre à la réponse HTTP appropriée en fonction de chaque demande à partir d’un client.

Cette rubrique peuvent être utile pour les développeurs de travaillent sur des applications qui offrent multimédia protégé à l’aide de plusieurs DRMs, tels que PlayReady et Widevine. La rubrique vous explique comment configurer le service de remise de licence PlayReady avec les stratégies d’autorisation afin que seuls les clients autorisés peuvent recevoir des licences PlayReady ou Widevine. Il indique également comment utiliser le chiffrement de chiffrement dynamiques avec PlayReady ou Widevine DRM sur tiret.

>[AZURE.NOTE]Pour commencer à l’aide de chiffrement dynamiques, vous devez d’abord obtenir au moins une unité d’échelle (également appelé unité de diffusion en continu). Pour plus d’informations, voir [comment mettre à l’échelle d’un Service de support](media-services-portal-manage-streaming-endpoints.md).


##<a name="download-sample"></a>Télécharger l’exemple

Vous pouvez télécharger l’exemple décrit dans cet article à partir [d’ici](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configuration dynamique chiffrement courantes et des Services de remise de licence DRM

Voici les étapes générales que vous devez effectuer lors de la protection de vos ressources avec PlayReady, en utilisant le service de remise de licence Media Services, ainsi que le chiffrement dynamique.

1. Créer un bien et télécharger des fichiers dans l’actif.
1. Codage de l’élément contenant le fichier à la vitesse de transmission adaptive que MP4 définir.
1. Créer une clé de contenu et l’associer à l’actif codé. Dans les Services de support, la clé de contenu contient clé de chiffrement de l’actif.
1. Configurer la stratégie d’autorisation de la clé de contenu. La stratégie d’autorisation clé contenu doit être configurée par vous et remplie par le client afin que la clé de contenu doit être remis au client.

Lorsque vous créez la stratégie d’autorisation clé contenu, vous devez spécifier les éléments suivants : méthode (PlayReady ou Widevine), les restrictions de remise (ouvertes ou jetons) et des informations spécifiques au type de remise clés qui définit la manière dont la clé est envoyée au client (modèle de licence[PlayReady](media-services-playready-license-template-overview.md) ou [Widevine](media-services-widevine-license-template-overview.md) ).
1. Configurer la stratégie de remise pour un bien. Configuration de la stratégie de remise comprend : protocole de remise (par exemple, MPEG tiret, TLS, HDS, Smooth Streaming ou tous), le type de chiffrement dynamiques (par exemple, courantes chiffrement) PlayReady ou URL d’acquisition de licence Widevine.

Vous pouvez appliquer des stratégies différents pour chaque protocole sur le même actif. Par exemple, vous pouvez appliquer le chiffrement PlayReady lisse/tiret et AES enveloppe à TLS. Les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, vous ajoutez une stratégie unique qui spécifie uniquement TLS comme protocole) ne pourront pas la diffusion. L’exception à cette règle est si vous n’avez aucune stratégie de remise de biens tout défini. Ensuite, tous les protocoles seront autorisés en clair.
1. Créer un repère à la demande afin d’obtenir une URL de diffusion en continu.

Vous trouverez un exemple .NET complet à la fin de la rubrique.

L’image suivante illustre le flux de travail décrit ci-dessus. Ici, le jeton est utilisé pour l’authentification.

![Protéger avec PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Le reste de cette rubrique fournit des explications détaillées, des exemples de code et des liens vers des rubriques qui vous montrent comment effectuer les tâches décrites ci-dessus.

##<a name="current-limitations"></a>Limitations en cours

Si vous ajoutez ou mettez à jour une stratégie de remise de biens, vous devez supprimer le repère associé (le cas échéant) et créer une nouveau locator.

Limitation lors du chiffrement avec Widevine avec Azure Media Services : actuellement, plusieurs touches contenus ne sont pas pris en charge.

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>Créer un bien et télécharger des fichiers dans l’actif

Afin de gérer, coder et diffuser vos vidéos, vous devez tout d’abord télécharger votre contenu dans Microsoft Azure Media Services. Une fois téléchargé, votre contenu est stocké en toute sécurité dans le nuage pour un traitement approfondi et de diffusion en continu.

Pour plus d’informations, voir [Télécharger des fichiers dans un compte Media Services](media-services-dotnet-upload-files.md).

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codage de l’élément contenant le fichier à la vitesse de transmission adaptive que MP4 définir

Avec chiffrement dynamiques vous avez seulement besoin consiste à créer un bien qui contient un jeu de fichiers MP4 multi-débit ou multi-débit Smooth Streaming des fichiers source. Puis, selon le format spécifié dans la demande de manifeste et fragment, le serveur de diffusion en continu à la demande assuré de recevoir le flux de données dans le protocole que vous avez choisi. Par conséquent, vous ne devez stocker et payer pour les fichiers au format de stockage unique et services de support pour la génération et répondre à la réponse appropriée basée sur les requêtes à partir d’un client. Pour plus d’informations, consultez la rubrique [Vue d’ensemble emballage dynamique](media-services-dynamic-packaging-overview.md) .

Pour obtenir des instructions sur la façon de coder, voir [comment encoder un bien à l’aide de Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).


##<a id="create_contentkey"></a>Créer une clé de contenu et l’associer à l’actif codé

Dans les Services de support, la clé de contenu contient la clé que vous voulez chiffrer un bien avec.

Pour plus d’informations, voir [Créer contenu clé](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurer la stratégie d’autorisation de la clé de contenu

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation clé contenu doit être configurée par vous et remplie par le client (lecteur) dans l’ordre de la clé pour être remis au client. La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou restriction des jetons.

Pour plus d’informations, voir [Configurer la stratégie d’autorisation de clé de contenu](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurer la stratégie de remise de biens 

Configurer la stratégie de remise pour votre actif. Certains éléments qui inclut la configuration de stratégie de remise de biens :

- L’URL d’acquisition de licence DRM. 
- Le protocole de livraison actif (par exemple, MPEG tiret, TLS, HDS, Smooth Streaming ou tout). 
- Le type de chiffrement dynamiques (dans ce cas, le chiffrement courantes). 

Pour plus d’informations, voir [définir les stratégies de remise de biens ](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Créer une demande de diffusion en continu locator afin d’obtenir une URL de diffusion en continu

Vous devrez fournir aux utilisateurs l’URL de diffusion en continu lisse, tiret ou TLS.

>[AZURE.NOTE]Si vous ajoutez ou mettez à jour la stratégie de remise de vos biens, vous devez supprimer un repère existant (le cas échéant) et créer une nouveau locator.

Pour obtenir des instructions sur la façon de publier un bien et générer une URL de diffusion en continu, voir [créer une URL de diffusion en continu](media-services-deliver-streaming-content.md).

##<a name="get-a-test-token"></a>Obtenir un test jeton

Obtenir un test jeton en fonction de la restriction jeton qui a été utilisée pour la stratégie d’autorisation clés.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

    
Vous pouvez utiliser le [Lecteur AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) pour tester votre flux de données.

##<a id="example"></a>Exemple


L’exemple suivant illustre la fonctionnalité a été introduite dans Azure Media Services SDK pour .net-Version 3.5.2 (en particulier, la possibilité de définir un modèle de licence Widevine et demander une licence Widevine à partir d’Azure Media Services). La commande de package Nuget suivante a été utilisée pour installer le package :

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Créer un nouveau projet Console.
1. Utilisez NuGet pour installer et ajouter Azure Media Services .NET SDK.
2. Ajouter des références : System.Configuration.
2. Ajouter le fichier de configuration qui contient le nom de compte et les informations de clé :
    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. Accéder au moins une unité de diffusion en continu du point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu. Pour plus d’informations, voir : [configurer les points de terminaison de diffusion en continu](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Remplacer le code dans votre fichier Program.cs par le code indiqué dans cette section.
    
    Veillez à mettre à jour variables pour pointer vers les dossiers où se trouvent vos fichiers d’entrée.
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
                    Console.ReadLine();
                }
        
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
        
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
        
                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
        
                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }
        
                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;
        
                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
        
                    responseTemplate.LicenseTemplates.Add(licenseTemplate);
        
                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                
                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice, 
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
            
                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;
        
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Voir aussi

[CENC avec Multi-DRM et de contrôle d’accès](media-services-cenc-with-multidrm-access-control.md)

[Configurer l’emballage Widevine avec AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Annonce de services de remise de licence Google Widevine dans Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
