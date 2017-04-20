<properties 
    pageTitle="Protéger votre contenu avec Apple FairPlay et/ou Microsoft PlayReady TLS | Microsoft Azure" 
    description="Cette rubrique offre une vue d’ensemble et montre comment utiliser Azure Media Services pour dynamiquement chiffrer votre contenu de diffusion en continu Live HTTP (TLS) avec Apple FairPlay. Il indique également comment utiliser le service de remise de licence Media Services pour fournir des licences FairPlay aux clients." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Protéger votre contenu avec Apple FairPlay et/ou Microsoft PlayReady TLS

Azure Media Services permet de façon dynamique chiffrer votre contenu de diffusion en continu Live HTTP (TLS) en utilisant les formats suivants :  

- **Touche effacer AES 128 enveloppe** 

    L’intégralité du morceau est chiffré à l’aide du mode **CBC AES 128** . Le déchiffrement du flux de données est pris en charge par iOS et le lecteur OSX en mode natif. Pour plus d’informations, voir [cet article](media-services-protect-with-aes128.md).

- **Apple FairPlay** 

    Les exemples audio et vidéo individuels sont chiffrés à l’aide du mode **CBC AES 128** . **FairPlay en continu** (I) est intégré dans les systèmes d’exploitation appareil, avec prise en charge native sur iOS et TV Apple. Safari sur OS X permet d’images par seconde à l’aide de la prise en charge des interface chiffrées Media Extensions (EME).
- **Microsoft PlayReady**

L’image suivante montre le flux de travail **TLS + FairPlay et/ou PlayReady chiffrement dynamiques** .

![Protéger avec FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Cette rubrique montre comment utiliser Azure Media Services pour dynamiquement chiffrer votre contenu TLS avec Apple FairPlay. Il indique également comment utiliser le service de remise de licence Media Services pour fournir des licences FairPlay aux clients.

>[AZURE.NOTE] Si vous souhaitez également chiffrer votre contenu TLS avec PlayReady, vous devez créer une clé de contenu courante et associer à votre actif. Vous devez également configurer la stratégie d’autorisation de la clé de contenu, comme décrit dans la rubrique de [chiffrement courantes dynamiques à l’aide de PlayReady](media-services-protect-with-drm.md) .

    
## <a name="requirements-and-considerations"></a>Configuration requise et remarques

- Les éléments suivants sont nécessaires pour utiliser AMS pour offrir TLS chiffré avec FairPlay et d’effectuer des licences FairPlay.

    - Un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F).
    - Un compte Media Services. Pour créer un compte Media Services, voir [Créer un compte](media-services-portal-create-account.md).
    - Inscrivez-vous avec [Le programme de développement Apple](https://developer.apple.com/).
    - Apple nécessite le propriétaire du contenu obtenir le [package de déploiement](https://developer.apple.com/contact/fps/). Indiquer la demande que vous déjà implémentée KSM (Module de sécurité clé) avec Azure Media Services et que vous demandez le package i final. Il y ait instructions dans le package i finale pour générer certification et obtenir ASK, qui vous servira à configurer FairPlay. 

    - Azure Media Services .NET SDK version **3.6.0** ou version ultérieure.

- Les éléments suivants doivent être définies sur le côté de remise clés AMS :
    - **Application certificat (AC)** - fichier .pfx contenant clé privée. Ce fichier est créé par le client et chiffré avec un mot de passe par le même client. 
        
        Lorsque le client configure une stratégie de remise clé, ils doivent fournir ce mot de passe et la .pfx au format en base 64.

        Les étapes suivantes décrivent comment faire pour générer un certificat pfx pour FairPlay.
        
        1. Installer OpenSSL à partir de https://slproweb.com/products/Win32OpenSSL.html
        
            Accédez au dossier où se trouvent le certificat FairPlay et autres fichiers fournis par Apple.
        
        2. Ligne de commande pour convertir la limitée pem :
        
            « C:\OpenSSL-Win32\bin\openssl.exe » x509-informer der-dans fairplay.cer-arrière fairplay out.pem
        
        3. Ligne de commande pour convertir pem pfx avec la clé privée (le mot de passe du fichier pfx est alors invité par OpenSSL).
        
            « C:\OpenSSL-Win32\bin\openssl.exe » pkcs12-exportez - fairplay out.pfx-privatekey.pem inkey-dans fairplay out.pem - passin file:privatekey-pem-pass.txt 
        
    - **Mot de passe de certificat d’application** - Customer mot de passe pour le fichier .pfx créé.
    - **Code de mot de passe d’application certificat** - le client doit télécharger le mot de passe similaire à comment ils téléchargent d’autres touches AMS et à l’aide de la valeur d’énumération **ContentKeyType.FairPlayPfxPassword** . Par conséquent, ils obtiendront id AMS qu'il s’agit qu’ils doivent utiliser à l’intérieur de l’option de stratégie de remise clés.
    - **iv** - valeur aléatoire 16 octets, doit correspondre à la iv dans la stratégie de remise de biens. Client génère la IV et le place dans les deux emplacements : biens stratégie et comme clé remise stratégie option de distribution. 
    - **ASK** - ASK (Application Secret clé) correctement après réception lorsque vous générez la certification à l’aide du portail pour les développeurs Apple. Chaque équipe de développement recevront un ASK unique. Enregistrez une copie de la poser et stockez-le dans un emplacement sécurisé. Vous devrez configurer ASK comme FairPlayAsk à Azure Media Services ultérieurement. 
    -  **Demandez à un ID** - obtenues lorsque le client télécharge ASk dans AMS. Le client doit télécharger demander à l’aide de la valeur d’énumération **ContentKeyType.FairPlayASk** . Par conséquent, l’id AMS est retourné et c’est ce qui doit être utilisé lorsque vous définissez l’option de stratégie de remise clés.

- Les éléments suivants doivent être définies par le côté client i :
    - **Application certificat (AC)** -.cer/.der fichier contenant une clé publique qui utilise le système d’exploitation pour chiffrer certains charge utile. AMS doit connaître, car elle est requise par le lecteur. Le service de remise clés déchiffre à l’aide de la clé privée correspondante.

- Pour un flux de données chiffré FairPlay la lecture, vous devez obtenir ASK réel première et puis générer un certificat réel. Ce processus crée tous les 3 parties :

    -  .der, 
    -  .pfx et 
    -  le mot de passe pour la .pfx.
 
- Les clients qui prennent en charge TLS avec chiffrement **AES 128 CBC** : Safari sur OS X, TV Apple iOS.

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Étapes de configuration FairPlay le chiffrement et licence remise services dynamiques

Voici les étapes générales que vous devez effectuer lors de la protection de vos ressources avec FairPlay, en utilisant le service de remise de licence Media Services, ainsi que le chiffrement dynamique.

1. Créer un bien et télécharger des fichiers dans l’actif. 
1. Codage de l’élément contenant le fichier à la vitesse de transmission adaptive que MP4 définir.
1. Créer une clé de contenu et l’associer à l’actif codé.  
1. Configurer la stratégie d’autorisation de la clé de contenu. Lorsque vous créez la stratégie d’autorisation clé contenu, vous devez spécifier les éléments suivants : 
    
    - mode de remise (dans ce cas, FairPlay) 
    - Configuration des options FairPlay stratégie. Pour plus d’informations sur la configuration FairPlay, voir méthode ConfigureFairPlayPolicyOptions() dans l’exemple ci-dessous.
    
        >[AZURE.NOTE] En règle générale, vous pouvez configurer les options de stratégie FairPlay qu’une seule fois, étant donné que vous n’avez un ensemble de certification et ASK.
-restrictions (ouverts ou jetons), - et des informations spécifiques sur le type de livraison clés qui définit la manière dont la clé est envoyée au client. 
    
2. Configurer la stratégie de remise de biens. Configuration de la stratégie de remise inclut : 

    - protocole de livraison (TLS) 
    - le type de chiffrement dynamiques (chiffrement CBC courantes), 
    - URL d’acquisition de licences. 
    
    >[AZURE.NOTE]Si vous voulez envoyer un flux de données chiffrées avec FairPlay + DRM un autre, vous devez configurer des stratégies de remise distinct 
    >
    >- Un seul IAssetDeliveryPolicy configurer tiret avec CENC (PlayReady + WideVine) et lisse avec PlayReady. 
    >- Un autre IAssetDeliveryPolicy pour configurer FairPlay pour TLS

1. Créer un repère à la demande pour obtenir une URL de diffusion en continu.

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>À l’aide de remise clée FairPlay par les applications client/lecteur

Clients pourraient développez des applications de lecteur à l’aide du Kit de développement logiciel iOS. Pour être en mesure de lire le contenu FairPlay les clients doivent implémenter protocole d’échange de licence. Le protocole d’échange de licence n’est pas spécifié par Apple. C’est l’envoi de demandes de livraison clé à chaque application. Les services de livraison clés AMS FairPlay attend SPC à venir en tant que message de billet codé www-form-url sous la forme suivante : 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player ne prend pas en charge la lecture de FairPlay prêts à l’emploi. Les clients doivent obtenir le lecteur de l’exemple de compte du développeur Apple pour obtenir FairPlay lecture sur MAC OSX. 
 
##<a name="streaming-urls"></a>URL de diffusion en continu

Si votre bien a été chiffré avec plusieurs DRM, vous devez utiliser une balise de chiffrement dans l’URL de diffusion en continu : (format = 'm3u8-aapl', le chiffrement = « xxx »).

Les considérations suivantes s’appliquent :

- Uniquement zéro ou un type de chiffrement peut être spécifié.
- Type de chiffrement ne doit être indiquée dans l’url si seule une chiffrement a été appliqué à la ressource.
- Type de chiffrement respecte la casse.
- Vous pouvez spécifier les types de chiffrement suivants :  
    - **cenc**: chiffrement courantes (Playready ou Widevine)
    - **liste des CBC aapl**: Fairplay
    - **CBC**: le chiffrement AES enveloppe.


##<a name="net-example"></a>Dans l’exemple .NET


L’exemple suivant illustre la fonctionnalité a été introduite dans Azure Media Services SDK pour .net-Version 3.6.0 (la fonctionnalité permettant d’utiliser Azure Media Services pour présenter votre contenu chiffré avec FairPlay). La commande de package Nuget suivante a été utilisée pour installer le package :

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Créer un projet Console.
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
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
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
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
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
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
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
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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


##<a name="next-steps-media-services-learning-paths"></a>Étapes suivantes : Media Services rubriques d’apprentissage professionnelles

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
