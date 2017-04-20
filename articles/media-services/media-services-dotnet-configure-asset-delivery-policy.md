<properties 
    pageTitle="Configurer des stratégies de remise de biens avec .NET SDK | Microsoft Azure" 
    description="Cette rubrique indique comment configurer des stratégies de remise différents biens avec Azure Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurer des stratégies de remise de biens avec .NET SDK
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>Vue d’ensemble

Si vous envisagez de biens remise chiffrée, effectuez les étapes décrites dans le flux de travail de distribution de contenu Media Services consiste à configurer des stratégies de remise pour actifs. La stratégie de remise de biens indique Media Services souhaité pour vos biens à livrer : dans la diffusion en continu de protocole doit votre bien être dynamiquement empaqueté (par exemple, MPEG tiret, TLS, Smooth Streaming ou tous), si vous souhaitez dynamiquement chiffrer vos biens ou non et la manière dont (enveloppe ou le chiffrement courantes).

Cette rubrique explique pourquoi et comment créer et configurer des stratégies de remise de biens.

>[AZURE.NOTE]Pour pouvoir utiliser emballage dynamique et le chiffrement dynamique, vous devez vous assurer d’avoir au moins une échelle unité (également appelé diffusion en continu). Pour plus d’informations, voir [comment mettre à l’échelle d’un Service de support](media-services-portal-manage-streaming-endpoints.md).
>
>En outre, vos biens doit contenir un ensemble de débit adaptive MP4s ou débit adaptive Smooth Streaming fichiers.

Vous pouvez appliquer des stratégies différentes au même actif. Par exemple, vous pouvez appliquer le chiffrement PlayReady chiffrement Smooth Streaming et AES enveloppe MPEG tiret et TLS. Les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, vous ajoutez une stratégie unique qui spécifie uniquement TLS comme protocole) ne pourront pas la diffusion. L’exception à cette règle est si vous n’avez aucune stratégie de remise de biens tout défini. Ensuite, tous les protocoles seront autorisés en clair.

Si vous voulez effectuer une gestion du stockage chiffré, vous devez configurer stratégie de remise de l’actif. Avant de vos biens peuvent être diffusés en continu, le serveur de diffusion supprime le chiffrement de stockage et flux de données votre contenu à l’aide de la stratégie de livraison spécifiée. Par exemple, pour présenter votre bien chiffré avec une clé de chiffrement enveloppe Standard (AES), définissez le type de stratégie à **DynamicEnvelopeEncryption**. Pour supprimer le chiffrement de stockage et flux du bien en clair, définissez le type de stratégie à **NoDynamicEncryption**. Exemples qui montrent comment configurer ces types de stratégie suivent.

En fonction de la configuration de la stratégie de remise bien que vous pourrez dynamiquement, dynamiquement chiffrer et package de flux les protocoles de diffusion en continu suivants : flux Smooth Streaming, TLS, MPEG tiret et HDS.

La liste suivante présente les formats que vous utilisez pour transmettre en continu lisse, TLS, tiret et HDS.

Smooth Streaming :

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

TLS :

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

TIRET MPEG

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HDS

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Pour obtenir des instructions sur la façon de publier un bien et générer une URL de diffusion en continu, voir [créer une URL de diffusion en continu](media-services-deliver-streaming-content.md).

##<a name="considerations"></a>Considérations relatives à la

- Vous ne pouvez pas supprimer un AssetDeliveryPolicy associés lié une ressource alors qu’un repère à la demande (en continu) existe pour cette ressource. Il est recommandé de supprimer la stratégie de l’actif avant de le supprimer de la stratégie.
- Impossible de créer un repère de diffusion en continu sur gestion du stockage chiffré lorsqu’aucune stratégie de remise de biens est définie.  Si l’actif n’est pas stockage chiffré, le système vous permettra créer un repère et flux du bien en clair sans une stratégie de remise de biens.
- Vous pouvez avoir plusieurs stratégies de remise de biens associés liés une ressource unique, mais vous ne pouvez spécifier un moyen de gérer un AssetDeliveryProtocol donné.  Ce qui signifie que si vous essayez de lier deux stratégies de remise qui spécifient le protocole AssetDeliveryProtocol.SmoothStreaming provoquera une erreur, car le système ne sait pas laquelle celui que vous voulez qu’elle s’applique lorsqu’un client effectue une demande de Smooth Streaming.
- Si vous avez un bien avec une diffusion en continu locator existante, vous ne pouvez attacher une nouvelle stratégie du bien (vous pouvez supprimer le lien d’une stratégie existante à partir de l’actif, ou mettre à jour une stratégie de remise associés liée la ressource).  Vous devez d’abord supprimer l’URL de diffusion en continu, ajustez les stratégies, puis recréez la diffusion en continu locator.  Vous pouvez utiliser la même locatorId lorsque vous recréez l’URL de diffusion en continu, mais vous devez vous assurer que n’entraîner des problèmes pour les clients dans la mesure où le contenu peut être mis en cache à l’origine ou à un CDN en aval.


##<a name="clear-asset-delivery-policy"></a>Stratégie de remise de biens effacer

La méthode **ConfigureClearAssetDeliveryPolicy** suivante spécifie s’applique ne pas de chiffrement dynamiques et d’effectuer le flux dans un des protocoles suivants : protocoles MPEG tiret, TLS et Smooth Streaming. Vous souhaiterez peut-être appliquer cette stratégie à vos ressources de stockage chiffré.

Pour plus d’informations sur les valeurs que vous pouvez spécifier lorsque vous créez un AssetDeliveryPolicy, consultez la section [Types utilisés lorsque vous définissez AssetDeliveryPolicy](#types) .

statique public void ConfigureClearAssetDeliveryPolicy (biens IAsset) {IAssetDeliveryPolicy stratégie = _context. AssetDeliveryPolicies.Create (« Stratégie claire », AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null) ;

actif. DeliveryPolicies.Add(policy) ; }

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Stratégie de remise de biens DynamicCommonEncryption


La méthode **CreateAssetDeliveryPolicy** suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le chiffrement courantes dynamique (**DynamicCommonEncryption**) à un protocole de diffusion en continu lisse (autres protocoles seront bloquées à partir de la diffusion en continu). La méthode prend deux paramètres : **actif** (la ressource à laquelle vous voulez appliquer la stratégie de remise) et **IContentKey** (la clé de contenu du type **CommonEncryption** , pour plus d’informations, voir : [Création d’une clé de contenu](media-services-dotnet-create-contentkey.md#common_contentkey)).

Pour plus d’informations sur les valeurs que vous pouvez spécifier lorsque vous créez un AssetDeliveryPolicy, consultez la section [Types utilisés lorsque vous définissez AssetDeliveryPolicy](#types) .


statique CreateAssetDeliveryPolicy public void (IAsset biens, clé IContentKey) {Uri acquisitionUrl = clé. GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense) ;

Dictionnaire < AssetDeliveryPolicyConfigurationKey, string > assetDeliveryPolicyConfiguration = nouveau dictionnaire < AssetDeliveryPolicyConfigurationKey, chaîne > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}} ;

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure Media Services vous permet également d’ajouter le chiffrement Widevine. L’exemple suivant montre PlayReady et Widevine ajouté à la stratégie de remise de biens.


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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Lors du chiffrement avec Widevine, vous pourrez effectuer à l’aide de tiret. Vérifiez que vous spécifiez tiret dans le protocole de livraison actif.


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Stratégie de remise de biens DynamicEnvelopeEncryption 

La méthode **CreateAssetDeliveryPolicy** suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le chiffrement enveloppe dynamique (**DynamicEnvelopeEncryption**) aux protocoles Smooth Streaming, TLS et tiret (si vous décidez de ne pas spécifier certains protocoles, ils seront bloquées à partir de la diffusion en continu). La méthode prend deux paramètres : **actif** (la ressource à laquelle vous voulez appliquer la stratégie de remise) et **IContentKey** (la clé de contenu du type **EnvelopeEncryption** , pour plus d’informations, voir : [Création d’une clé de contenu](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Pour plus d’informations sur les valeurs que vous pouvez spécifier lorsque vous créez un AssetDeliveryPolicy, consultez la section [Types utilisés lorsque vous définissez AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
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
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Types d’utilisés lorsque vous définissez AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


