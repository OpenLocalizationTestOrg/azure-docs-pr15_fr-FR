<properties 
    pageTitle="Configurer la stratégie d’autorisation clé contenu à l’aide de Media Services .NET SDK | Microsoft Azure" 
    description="Découvrez comment configurer une stratégie d’autorisation pour une clé de contenu à l’aide de Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;mingfeiy"/>



# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Chiffrement dynamiques : configurer la stratégie d’autorisation clé contenu

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services vous permet d’effectuer des flux MPEG-tiret, Smooth Streaming et diffusion Live HTTP (TLS) protégés par le chiffrement AES (Advanced Standard) (à l’aide de clés de chiffrement 128 bits) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS vous permet également d’effectuer des flux tiret chiffrées avec Widevine DRM. PlayReady et Widevine sont chiffrés par la spécification de chiffrement courantes (ISO/IEC 23001-7 CENC).

Media Services fournit également un **Service de remise de clé/licence** à partir de laquelle les clients peuvent-ils clés AES ou PlayReady/Widevine des licences pour lire le contenu chiffré.

Si vous souhaitez que pour les Services de support chiffrer un bien, vous devez associer une clé de chiffrement (**CommonEncryption** ou **EnvelopeEncryption**) du bien (comme décrit [ici](media-services-dotnet-create-contentkey.md)) et également configurer les stratégies d’autorisation pour la clé (comme décrit dans cet article).

Lorsqu’un flux de données est demandé par un lecteur, Media Services utilise la clé spécifiée pour dynamiquement chiffrer votre contenu en utilisant le chiffrement AES ou DRM. Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de remise clés. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : **Ouvrir** ou **jeton** restriction. La stratégie restreint jeton doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de **Jetons Web Simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) et **JSON Web jeton** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Media Services ne fournit pas de Services de jetons banque d’informations sécurisé. Vous pouvez créer un STS personnalisé ou tirer parti de Microsoft Azure ACS aux jetons de problème. Le STS doit être configuré pour créer un jeton connecté avec les revendications spécifiées clé et le problème que vous avez spécifié dans la configuration de la restriction jeton (comme décrit dans cet article). Le service de remise clés Media Services renvoie la clé de chiffrement au client si le jeton est valide et les revendications dans le jeton correspondent à ceux qui sont configurés pour la clé de contenu.

Pour plus d’informations, voir

[Authenitcation jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Intégrer Azure Media Services OWIN MVC en fonction de l’application avec Azure Active Directory et de limiter la remise de clés contenue basée sur les revendications JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Utiliser Azure ACS aux jetons de problème](http://mingfeiy.com/acs-with-key-services).

###<a name="some-considerations-apply"></a>Certaines considérations s’appliquent :

- Pour pouvoir utiliser emballage dynamique et le chiffrement dynamique, vous devez vous assurer d’avoir au moins une unité réservée la diffusion. Pour plus d’informations, voir [comment mettre à l’échelle d’un Service de support](media-services-portal-manage-streaming-endpoints.md).
- Vos biens doit contenir un ensemble de débit adaptive MP4s ou débit adaptive Smooth Streaming fichiers. Pour plus d’informations, voir [Encoder un bien](media-services-encode-asset.md).
- Téléchargez et coder vos biens à l’aide d’option **AssetCreationOptions.StorageEncrypted** .
- Si vous envisagez d’avoir plusieurs clés de contenu qui nécessitent la même configuration de la stratégie, il est recommandé pour créer une stratégie d’autorisation unique et réutiliser avec plusieurs touches contenus.
- Le service de remise de clé met en cache ContentKeyAuthorizationPolicy et ses objets associés (options de stratégie et restrictions) pendant 15 minutes.  Si vous créez un ContentKeyAuthorizationPolicy et que vous spécifiez pour utiliser une restriction « Jeton », puis tester et puis mettez à jour la stratégie à la restriction « Ouvrir », vous devrez patienter environ 15 minutes avant que la stratégie bascule vers la version « Ouverte » de la stratégie.
- Si vous ajoutez ou mettez à jour la stratégie de remise de vos biens, vous devez supprimer un repère existant (le cas échéant) et créer une nouveau locator.
- Pour l’instant, vous ne pouvez pas chiffrer HDS streaming format ou progressive télécharge.


##<a name="aes-128-dynamic-encryption"></a>Chiffrement dynamiques AES 128

###<a name="open-restriction"></a>Restriction ouvrir

La restriction Ouvrir signifie que le système doit apporter la clé à une autre personne qui la crée une demande de clé. Cette restriction peut être utile pour des fins de test.

L’exemple suivant crée une stratégie d’autorisation ouverte et ajoute à la clé de contenu.

statique public void AddOpenAuthorizationPolicy (IContentKey contentKey) {/ / créer ContentKeyAuthorizationPolicy avec des restrictions ouvre / / et créer la stratégie d’autorisation IContentKeyAuthorizationPolicy stratégie = _context.
ContentKeyAuthorizationPolicies.
CreateAsync (« stratégie d’autorisation ouverte »). Erreur est générée.

Liste<ContentKeyAuthorizationPolicyRestriction> restrictions = nouvelle liste<ContentKeyAuthorizationPolicyRestriction>() ;
    
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


###<a name="token-restriction"></a>Restriction jeton

Cette section décrit comment créer une stratégie d’autorisation clé contenu et l’associer à la clé de contenu. La stratégie d’autorisation décrit les exigences d’autorisation doivent être remplies pour déterminer si l’utilisateur est autorisé à recevoir la clé (par exemple, est la liste « clé de vérification » contiennent la clé de signature avec le jeton).

Pour configurer l’option de restriction jeton, vous devez utiliser un code XML pour décrire les exigences d’autorisation du jeton. La configuration de la restriction jeton XML doit être conformes au schéma XML suivant.

####<a id="schema"></a>Schéma de restriction jetons
    
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Lors de la configuration du **jeton** restreint stratégie, vous devez spécifier les paramètres de** clé de vérification**, **émetteur** et **public** principales. La **clé primaire vérification **contient la clé de signature avec le jeton, **émetteur** est le service de jeton sécurisé qui émet le jeton. L' **audience** (parfois appelée **étendue**) décrit l’objectif du jeton ou la ressource le jeton autorise l’accès à. Le service de remise clés Media Services valide que les valeurs suivantes dans le jeton correspondent aux valeurs dans le modèle. 

Lorsque vous utilisez **Media Services SDK pour .NET**, vous pouvez utiliser la classe **TokenRestrictionTemplate** pour générer le jeton de restriction.
L’exemple suivant crée une stratégie d’autorisation avec une restriction jeton. Dans cet exemple, le client devra présenter un jeton contenant : signature clé (VerificationKey), un jeton émetteur et revendications requises.
    
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

####<a id="test"></a>Jeton de test

Pour obtenir un jeton de test en fonction de la restriction jeton qui a été utilisée pour la stratégie d’autorisation clés, procédez comme suit.
    
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
    
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


##<a name="playready-dynamic-encryption"></a>Chiffrement PlayReady dynamiques 

Media Services vous permet de configurer les droits et restrictions que vous souhaitez pour le runtime PlayReady DRM mettent en œuvre lorsqu’un utilisateur tente de lire le contenu protégé. 

Lorsque vous protégez votre contenu avec PlayReady, un des choses que vous devez spécifier dans votre stratégie d’autorisation est une chaîne XML qui définit le [modèle de licence PlayReady](media-services-playready-license-template-overview.md). Dans Media Services SDK pour .NET, les classes **PlayReadyLicenseResponseTemplate** et **PlayReadyLicenseTemplate** vous aidera à définir le modèle de licence PlayReady.

[Cette rubrique](media-services-protect-with-drm.md) indique comment chiffrer votre contenu avec **PlayReady** et **Widevine**.

###<a name="open-restriction"></a>Restriction ouvrir
    
La restriction Ouvrir signifie que le système doit apporter la clé à une autre personne qui la crée une demande de clé. Cette restriction peut être utile pour des fins de test.

L’exemple suivant crée une stratégie d’autorisation ouverte et ajoute à la clé de contenu.

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
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
    
    
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

###<a name="token-restriction"></a>Restriction jeton

Pour configurer l’option de restriction jeton, vous devez utiliser un code XML pour décrire les exigences d’autorisation du jeton. La configuration de la restriction jeton XML doit être conformes au schéma XML présenté dans [cette](#schema) section.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
                
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
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


Pour obtenir un jeton de test en fonction de la restriction jeton qui a été utilisée pour l’autorisation clée stratégie voir [cette](#test) section. 

##<a id="types"></a>Types d’utilisés lorsque vous définissez ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Étape suivante
À présent que vous avez configuré la stratégie d’autorisation de clé de contenu, accédez à la rubrique [configurer la stratégie de remise de biens](media-services-dotnet-configure-asset-delivery-policy.md) .
 
