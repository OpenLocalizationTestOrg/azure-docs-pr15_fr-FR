<properties
    pageTitle="À l’aide de chiffrement dynamiques AES 128 et service de remise clés | Microsoft Azure"
    description="Microsoft Azure Media Services vous permet d’effectuer votre contenu chiffré avec les clés de chiffrement AES 128 bits. Media Services fournit également le service de remise de clé qui fournit des clés de chiffrement aux utilisateurs autorisés. Cette rubrique indique comment chiffrer avec AES 128 dynamiquement et utiliser le service de remise clés."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>À l’aide de chiffrement dynamiques AES 128 et service de remise clés

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Vue d’ensemble

>[AZURE.NOTE] Consultez [cette](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) vidéo offre un aperçu de la protection de votre contenu multimédia avec le chiffrement AES.

Microsoft Azure Media Services vous permet d’effectuer Http Live-diffusion en continu (TLS) et les flux lisses chiffrées avec chiffrement AES (Advanced Standard) (à l’aide de clés de chiffrement 128 bits). Media Services fournit également le service de remise de clé qui fournit des clés de chiffrement aux utilisateurs autorisés. Si vous souhaitez que pour les Services de support chiffrer un bien, vous devez associer une clé de chiffrement du bien et également configurer les stratégies d’autorisation pour la clé. Lorsqu’un flux de données est demandé par un lecteur, Media Services utilise la clé spécifiée pour dynamiquement chiffrer votre contenu en utilisant le chiffrement AES. Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de remise clés. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou restriction des jetons. La stratégie restreint jeton doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de [Jetons Web Simple](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) et [JSON Web jeton](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Pour plus d’informations, voir [configurer la stratégie d’autorisation de la clé de contenu](media-services-protect-with-aes128.md#configure_key_auth_policy).

Pour tirer parti de chiffrement dynamiques, vous devez disposer d’un bien qui contient un jeu de fichiers MP4 multi-débit ou multi-débit Smooth Streaming des fichiers source. Vous devez également configurer la stratégie de remise de l’actif (décrite plus loin dans cette rubrique). Puis, selon le format spécifié dans l’URL de diffusion en continu, le serveur de diffusion en continu à la demande garantit que le flux est remis dans le protocole que vous avez choisi. Par conséquent, vous ne devez stocker et payer pour les fichiers au format de stockage unique et services de support pour la génération et répondre à la réponse appropriée basée sur les requêtes à partir d’un client.

Cette rubrique peuvent être utile pour les développeurs de travaillent sur des applications qui offrent multimédia protégé. La rubrique vous explique comment configurer le service de remise clés avec les stratégies d’autorisation afin que seuls les clients autorisés peuvent recevoir les clés de chiffrement. Il indique également comment utiliser le chiffrement dynamique.

>[AZURE.NOTE]Pour commencer à l’aide de chiffrement dynamiques, vous devez d’abord obtenir au moins une unité d’échelle (également appelé unité de diffusion en continu). Pour plus d’informations, voir [comment mettre à l’échelle d’un Service de support](media-services-portal-manage-streaming-endpoints.md).

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Chiffrement dynamiques AES 128 et flux de travail clés remise Service

Voici les étapes générales que vous devez effectuer lors du chiffrement de vos ressources avec AES, en utilisant le service de remise clés Media Services, ainsi que le chiffrement dynamique.

1. [Créer un bien et télécharger des fichiers dans l’actif](media-services-protect-with-aes128.md#create_asset).
1. [Encoder du bien contenant le fichier à la vitesse de transmission adaptive que MP4 définir](media-services-protect-with-aes128.md#encode_asset).
1. [Créer une clé de contenu et y associer du bien codé](media-services-protect-with-aes128.md#create_contentkey). Dans les Services de support, la clé de contenu contient clé de chiffrement de l’actif.
1. [Configurer la stratégie d’autorisation de la clé de contenu](media-services-protect-with-aes128.md#configure_key_auth_policy). La stratégie d’autorisation clé contenu doit être configurée par vous et remplie par le client afin que la clé de contenu doit être remis au client.
1. [Configurer la stratégie de remise pour un bien](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configuration de stratégie de remise inclut : acquisition URL et vecteur d’initialisation (IV) clé (AES 128 nécessite la même IV à fournir lors du chiffrement et déchiffrement), protocole de remise (par exemple, MPEG tiret, TLS, HDS, Smooth Streaming ou tous), le type de chiffrement dynamiques (par exemple, enveloppe ou aucun chiffrement dynamiques).

Vous pouvez appliquer des stratégies différents pour chaque protocole sur le même actif. Par exemple, vous pouvez appliquer le chiffrement PlayReady lisse/tiret et AES enveloppe à TLS. Les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, vous ajoutez une stratégie unique qui spécifie uniquement TLS comme protocole) ne pourront pas la diffusion. L’exception à cette règle est si vous n’avez aucune stratégie de remise de biens tout défini. Ensuite, tous les protocoles seront autorisés en clair.

1. [Créer un repère à la demande](media-services-protect-with-aes128.md#create_locator) afin d’obtenir une URL de diffusion en continu.

Cette rubrique présente également [comment une application cliente peut demander une clé à partir du service de remise clés](media-services-protect-with-aes128.md#client_request).

Vous trouverez une.NET complet [exemple](media-services-protect-with-aes128.md#example) à la fin de la rubrique.

L’image suivante illustre le flux de travail décrit ci-dessus. Ici, le jeton est utilisé pour l’authentification.

![Protéger avec AES 128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Le reste de cette rubrique fournit des explications détaillées, des exemples de code et des liens vers des rubriques qui vous montrent comment effectuer les tâches décrites ci-dessus.

##<a name="current-limitations"></a>Limitations en cours

Si vous ajoutez ou mettez à jour la stratégie de remise de vos biens, vous devez supprimer un repère existant (le cas échéant) et créer une nouveau locator.

##<a id="create_asset"></a>Créer un bien et télécharger des fichiers dans l’actif

Afin de gérer, coder et diffuser vos vidéos, vous devez tout d’abord télécharger votre contenu dans Microsoft Azure Media Services. Une fois téléchargé, votre contenu est stocké en toute sécurité dans le nuage pour un traitement approfondi et de diffusion en continu. 

Pour plus d’informations, voir [Télécharger des fichiers dans un compte Media Services](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codage de l’élément contenant le fichier à la vitesse de transmission adaptive que MP4 définir

Avec chiffrement dynamiques vous avez seulement besoin consiste à créer un bien qui contient un jeu de fichiers MP4 multi-débit ou multi-débit Smooth Streaming des fichiers source. Puis, selon le format spécifié dans la demande manifeste ou fragment, la diffusion en continu à la demande serveur assuré de recevoir le flux de données dans le protocole que vous avez choisi. Par conséquent, vous ne devez stocker et payer pour les fichiers au format de stockage unique et services de support pour la génération et répondre à la réponse appropriée basée sur les requêtes à partir d’un client. Pour plus d’informations, consultez la rubrique [Vue d’ensemble emballage dynamique](media-services-dynamic-packaging-overview.md) .

Pour obtenir des instructions sur la façon de coder, voir [comment encoder un bien à l’aide de Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Créer une clé de contenu et l’associer à l’actif codé

Dans les Services de support, la clé de contenu contient la clé que vous voulez chiffrer un bien avec.

Pour plus d’informations, voir [Créer contenu clé](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configurer la stratégie d’autorisation de la clé de contenu

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation clé contenu doit être configurée par vous et remplie par le client (lecteur) dans l’ordre de la clé pour être remis au client. La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir, jeton restriction ou restriction IP.

Pour plus d’informations, voir [Configurer la stratégie d’autorisation de clé de contenu](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configurer la stratégie de remise de biens 

Configurer la stratégie de remise pour votre actif. Certains éléments qui inclut la configuration de stratégie de remise de biens :

- L’URL d’acquisition de clé. 
- Le vecteur d’initialisation (IV) à utiliser pour le chiffrement des enveloppes. AES 128 nécessite la même IV à fournir lors du chiffrement et déchiffrement. 
- Le protocole de livraison actif (par exemple, MPEG tiret, TLS, HDS, Smooth Streaming ou tout).
- Le type de chiffrement dynamiques (par exemple, enveloppe AES) ou aucun chiffrement dynamique. 

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

##<a id="client_request"></a>Comment votre client peut demander une clé à partir du service de remise clés ?

Dans l’étape précédente, vous avez construit l’URL qui pointe vers un fichier manifeste. Votre client doit extraire les informations nécessaires à partir des diffusion en continu fichiers manifeste afin de pouvoir pour modifier une demande au service de remise clés.

###<a name="manifest-files"></a>Fichiers manifeste

Le client doit extraire l’URL (contenant également contenu clé Id (enfants)) valeur à partir du fichier manifest. Le client essaiera puis obtenir la clé de chiffrement à partir du service de remise clés. Le client doit également extraire la valeur de VI et l’utilisation il déchiffrer le flux de données. L’extrait de code suivant le <Protection> élément du manifeste Smooth Streaming.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

Dans le cas de TLS, le manifeste racine est divisé en fichiers segment. 

Par exemple, le manifeste racine est : http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) et qu’il contient la liste des noms de fichiers de segment.
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Si vous ouvrez un fichier de segment dans l’éditeur de texte (par exemple, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), elle doit contenir #EXT-X-KEY qui indique que le fichier est chiffré.
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>Demander la clé à partir du service de remise clés

Le code suivant montre comment envoyer une demande au service de remise clés Media Services à l’aide d’une remise clée Uri (qui a été extrait à partir du manifeste) et un jeton (cette rubrique ne pas à propos pour récupérer les jetons Web Simple d’un Service Banque d’informations sécurisé de jetons).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>Exemple

1. Créer un nouveau projet Console.
1. Utilisez NuGet pour installer et ajouter les Extensions SDK .NET Azure Media Services. Installer ce package, également installe Media Services .NET SDK et ajoute tous les autres dépendances requises.
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

1. Remplacer le code dans votre fichier Program.cs par le code indiqué dans cette section.
    
    Veillez à mettre à jour variables pour pointer vers les dossiers où se trouvent vos fichiers d’entrée.
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
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
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
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
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "HLS Open Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null // no requirements needed for HLS
                                };
        
                    restrictions.Add(restriction);
        
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                        "policy",
                        ContentKeyDeliveryType.BaselineHttp,
                        restrictions,
                        "");
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("HLS token restricted authorization policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                            new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                            new ContentKeyAuthorizationPolicyRestriction
                            {
                                Name = "Token Authorization Policy",
                                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                Requirements = tokenTemplateString
                            };
        
                    restrictions.Add(restriction);
        
                    //You could have multiple options 
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                            "Token option for HLS",
                            ContentKeyDeliveryType.BaselineHttp,
                            restrictions,
                            null  // no key delivery data is needed for HLS
                            );
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
        
                    return tokenTemplateString;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
                    };
        
                    IAssetDeliveryPolicy assetDeliveryPolicy =
                        _context.AssetDeliveryPolicies.Create(
                                    "AssetDeliveryPolicy",
                                    AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                                    AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                                    assetDeliveryPolicyConfiguration);
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
                    Console.WriteLine();
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
