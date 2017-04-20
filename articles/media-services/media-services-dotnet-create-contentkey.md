<properties 
    pageTitle="Créer des ContentKeys avec .NET" 
    description="Apprenez à créer des clés de contenu qui fournissent un accès sécurisé aux ressources." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="create-contentkeys-with-net"></a>Créer des ContentKeys avec .NET

> [AZURE.SELECTOR]
- [RESTE](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

Media Services vous permet de créer et de livraison des documents chiffrés. Un **ContentKey** offre un accès sécurisé à votre s **actif**. 

Lorsque vous créez un nouvel actif (par exemple, avant de vous [télécharger des fichiers](media-services-dotnet-upload-files.md)), vous pouvez spécifier les options de chiffrement suivantes : **StorageEncrypted**, **CommonEncryptionProtected**ou **EnvelopeEncryptionProtected**. 

Lorsque vous effectuez des biens à vos clients, vous pouvez [configurer pour biens à chiffrer dynamiquement](media-services-dotnet-configure-asset-delivery-policy.md) avec l’un de la perte de deux suivants : **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Actifs chiffrés doivent être associées à des **ContentKey**s. Cet article décrit comment créer une clé de contenu.

>[AZURE.NOTE] Lorsque vous créez un nouvel actif **StorageEncrypted** à l’aide de Media Services .NET SDK, le **ContentKey** est automatiquement créé et liées à l’actif.

##<a name="contentkeytype"></a>ContentKeyType

Parmi les valeurs que vous devez définir quand créer un contenu clé est le type de contenu clé. Choisir l’une des valeurs suivantes. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Créer le type d’enveloppe ContentKey

L’extrait de code suivant crée une clé de contenu du type de chiffrement enveloppe. Il associe ensuite la clé à l’élément spécifié.

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

        asset.ContentKeys.Add(key);

        return key;
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

appel

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Créer un type commun ContentKey    

L’extrait de code suivant crée une clé de contenu du type de chiffrement courantes. Il associe ensuite la clé à l’élément spécifié.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
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
appel

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
