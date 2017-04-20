<properties 
    pageTitle="Comment effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-débit avec .NET | Microsoft Azure" 
    description="Ce didacticiel vous guide tout au long des étapes de création d’un canal qui reçoit un flux en direct unique débit et code au flux multi-débit à l’aide du Kit de développement .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Comment effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-débit avec .NET

> [AZURE.SELECTOR]
- [Portail](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F).

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel vous guide tout au long des étapes de création d’un **canal** qui reçoit un flux en direct unique débit et code au flux multi-débit.

Pour plus d’informations conceptuelles associés aux canaux activés pour le codage live, voir [Live en continu à l’aide d’Azure Media Services pour créer des flux multi-débit](media-services-manage-live-encoder-enabled-channels.md).


##<a name="common-live-streaming-scenario"></a>Scénario de diffusion en continu Live courant

Les étapes suivantes décrivent les tâches nécessaires à la création d’applications de diffusion en continu live courantes.

>[AZURE.NOTE] Pour l’instant, la durée maximale recommandée d’un événement en direct est 8 heures. Veuillez contacter amslived Microsoft.com si vous avez besoin exécuter un canal pour longtemps.

1. Connectez une caméra vidéo à un ordinateur. Lancer et configurer un encodeur live local qui peut afficher un flux de débit unique dans un des protocoles suivants : RTMP, Smooth Streaming ou RTP (MPEG-TS). Pour plus d’informations, voir [prise en charge du RTMP Azure Media Services et encodeurs Live](http://go.microsoft.com/fwlink/?LinkId=532824).

Cette étape peut également être effectuée après avoir créé votre canal.

1. Créer et commencer à un canal.

1. Récupérer le canal d’acquisition URL.

L’URL d’acquisition est utilisé par l’encodeur live pour envoyer le flux de données pour le canal.

1. Extraire l’URL d’aperçu de canal.

Utilisez cette URL pour vérifier que votre canal reçoit correctement le flux en direct.

2. Créer un bien.
3. Si vous voulez pour l’actif à chiffrer dynamiquement pendant la lecture, procédez comme suit :
1. Créer une clé de contenu.
1. Configurer la stratégie d’autorisation de la clé de contenu.
1. Configurer la stratégie de remise de biens (utilisée par emballage dynamique et le chiffrement dynamique).
3. Créez un programme et spécifiez pour l’utilisation de la ressource que vous avez créé.
1. Publier la ressource associée au programme en créant un repère à la demande.

Assurez-vous que vous disposez au moins une unité réservée sur le point de terminaison de diffusion en continu à partir duquel vous souhaitez transmettre du contenu de diffusion en continu.

1. Démarrez le programme lorsque vous êtes prêt à démarrer la diffusion en continu et d’archivage.
2. Vous pouvez également l’encodeur live peut être signalé pour démarrer une publication. La publication est insérée dans le flux de sortie.
1. Arrêter le programme chaque fois que vous souhaitez arrêter la diffusion et l’archivage de l’événement.
1. Supprimer le programme (et vous pouvez également supprimer l’actif).

## <a name="what-youll-learn"></a>Enseignements de cet article

Cette rubrique vous indique comment exécuter diverses opérations sur les canaux et les programmes à l’aide de Media Services .NET SDK. Étant donné que de nombreuses opérations sont longues API .NET qui gèrent les longues opérations sont utilisées.

La rubrique vous explique comment effectuer les opérations suivantes :

1. Créer et commencer à un canal. API longue sont utilisées.
1. Obtenir les canaux d’acquisition point de terminaison (input). Ce point de terminaison doit être fournie à l’encodeur qui peut envoyer un flux en direct débit unique.
1. Obtenir le point de terminaison (version préliminaire). Ce point de terminaison est utilisé pour afficher un aperçu de votre flux de données.
1. Créer un bien qui permet de stocker votre contenu. Les stratégies de remise de biens doivent être configurées également, comme illustré dans cet exemple.
1. Créez un programme et spécifiez pour l’utilisation de la ressource qui a été créée précédemment. Démarrez le programme. API longue sont utilisées.
1. Créer un repère de l’actif, afin que le contenu est publié et peut être transmis à vos clients.
1. Afficher et masquer des tablettes tactiles. Démarrer et arrêter les annonces. API longue sont utilisées.
1. Nettoyer votre canal et toutes les ressources associées.


##<a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont requis pour terminer le didacticiel.

- Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure.

Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez crédits pouvant être utilisées pour essayer services Azure payants. Même une fois que les crédits sont utilisés vers le haut, vous pouvez conserver le compte et utiliser des services Azure gratuits et fonctionnalités, telles que la fonctionnalité d’applications Web dans le Service d’application Azure.
- Un compte Media Services. Pour créer un compte Media Services, voir [Créer un compte](media-services-portal-create-account.md).
- Visual Studio 2010 SP1 (Professionnel, Premium, intégrale ou Express) ou versions ultérieures.
- Vous devez utiliser Media Services .NET SDK version 3.2.0.0 ou version ultérieure.
- Une webcam et un encodeur qui peut envoyer un flux en direct débit unique.

##<a name="considerations"></a>Considérations relatives à la

- Pour l’instant, la durée maximale recommandée d’un événement en direct est 8 heures. Veuillez contacter amslived Microsoft.com si vous avez besoin exécuter un canal pour longtemps.
- Assurez-vous que vous disposez au moins une unité réservée sur le point de terminaison de diffusion en continu à partir duquel vous souhaitez transmettre du contenu de diffusion en continu.

##<a name="download-sample"></a>Télécharger l’exemple

Obtenir et exécutez un échantillon de [ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##<a name="set-up-for-development-with-media-services-sdk-for-net"></a>Configurer pour le développement avec Media Services SDK pour .NET

1. Créer une application console à l’aide de Visual Studio.
1. Ajoutez le Kit de développement de Services de support pour .NET à votre application console à l’aide de package Media Services NuGet.

##<a name="connect-to-media-services"></a>Se connecter aux Services de support
Pour obtenir les meilleurs résultats, vous devez utiliser un fichier app.config pour stocker la clé de compte et nom de Media Services.

>[AZURE.NOTE]Pour rechercher le nom et la clé des valeurs, accédez au portail Azure et sélectionnez votre compte. La fenêtre Paramètres apparaît à droite. Dans la fenêtre Paramètres, sélectionnez clés. Cliquez sur l’icône en regard de chaque zone de texte copie la valeur dans le Presse-papiers système.

Ajoutez la section appSettings au fichier app.config et définissez les valeurs de votre clé de compte et nom de compte Media Services.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##<a name="code-example"></a>Exemple de code

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Recherchez d’autres informations ?

Si cette rubrique ne contenait pas ce que vous attendiez, il manque un élément, ou dans une autre façon n’a pas été répond à vos besoins, veuillez nous fournir vous commentaires à l’aide du thread Disqus ci-dessous.
