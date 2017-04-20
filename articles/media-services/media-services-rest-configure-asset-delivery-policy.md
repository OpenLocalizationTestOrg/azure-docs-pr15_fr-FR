<properties 
    pageTitle="Configuration des stratégies de remise de biens à l’aide de l’API REST Media Services | Microsoft Azure" 
    description="Cette rubrique indique comment configurer des stratégies de remise de biens différent à l’aide de l’API REST de Services de support." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>Configuration des stratégies de remise de biens

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Si vous envisagez d’effectuer des biens dynamiquement chiffrés, effectuez les étapes décrites dans le flux de travail de distribution de contenu Media Services consiste à configurer des stratégies de remise pour actifs. La stratégie de remise de biens indique Media Services souhaité pour vos biens à livrer : dans la diffusion en continu de protocole doit votre bien être dynamiquement empaqueté (par exemple, MPEG tiret, TLS, Smooth Streaming ou tous), si vous souhaitez dynamiquement chiffrer vos biens ou non et la manière dont (enveloppe ou le chiffrement courantes).

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
- Si vous avez un bien avec une diffusion en continu locator existante, vous ne peut pas lier une nouvelle stratégie de la ressource, dissocier une stratégie existante à partir de l’actif ou mettre à jour une stratégie de remise associés liée la ressource.  Vous devez d’abord supprimer l’URL de diffusion en continu, ajustez les stratégies, puis recréez la diffusion en continu locator.  Vous pouvez utiliser la même locatorId lorsque vous recréez l’URL de diffusion en continu, mais vous devez vous assurer que n’entraîner des problèmes pour les clients dans la mesure où le contenu peut être mis en cache à l’origine ou à un CDN en aval.

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services de support, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez entités dans Media Services, vous devez définir en-tête spécifique champs et des valeurs dans vos requêtes HTTP. Pour plus d’informations, voir [le programme d’installation pour le développement d’API REST Media Services](media-services-rest-how-to-use.md).

>Une fois connecté avec succès à https://media.windows.net, vous recevrez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI comme décrit dans la [connexion à Media Services à l’aide de l’API REST](media-services-rest-connect-programmatically.md).


##<a name="clear-asset-delivery-policy"></a>Stratégie de remise de biens effacer

###<a id="create_asset_delivery_policy"></a>Créer la stratégie de remise de biens
La requête HTTP suivante crée une stratégie de remise de biens qui spécifie s’applique ne pas de chiffrement dynamiques et d’effectuer le flux dans un des protocoles suivants : protocoles MPEG tiret, TLS et Smooth Streaming. 

Pour plus d’informations sur les valeurs que vous pouvez spécifier lorsque vous créez un AssetDeliveryPolicy, consultez la section [Types utilisés lorsque vous définissez AssetDeliveryPolicy](#types) .   


Demande :
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Réponse :
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>Bien lien avec la stratégie de remise de biens

La requête HTTP suivante lie le bien spécifié à la stratégie de remise de biens pour.

Demande :

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Réponse :

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Stratégie de remise de biens DynamicEnvelopeEncryption 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Créer la clé de contenu du type EnvelopeEncryption et liez-le à l’actif

Lorsque vous spécifiez DynamicEnvelopeEncryption remise stratégie, vous devez veillez à lier vos biens à une clé de contenu du type EnvelopeEncryption. Pour plus d’informations, voir : [Création d’une clé de contenu](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Obtenir l’URL de remise

Obtenir l’URL de remise pour le mode de remise spécifié de la clé de contenu créé à l’étape précédente. Un client utilise l’URL retournée pour demander une clé AES ou un PlayReady licence dans l’ordre de lecture du contenu protégé.

Spécifier le type de l’URL pour accéder dans le corps de la requête HTTP. Si vous protégez votre contenu avec PlayReady, demander une URL d’acquisition de licence PlayReady de Services de support, à l’aide de 1 pour le keyDeliveryType : {« keyDeliveryType » : 1}. Si vous protégez votre contenu avec le chiffrement enveloppe, demander une URL clé d’acquisition en spécifiant 2 pour keyDeliveryType : {« keyDeliveryType » : 2}.

Demande :
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

Réponse :
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>Créer la stratégie de remise de biens

La requête HTTP suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le chiffrement enveloppe dynamique (**DynamicEnvelopeEncryption**) pour le protocole **TLS** (dans cet exemple, autres protocoles seront bloquées à partir de la diffusion en continu). 


Pour plus d’informations sur les valeurs que vous pouvez spécifier lorsque vous créez un AssetDeliveryPolicy, consultez la section [Types utilisés lorsque vous définissez AssetDeliveryPolicy](#types) .   

Demande :

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
Réponse :
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>Bien lien avec la stratégie de remise de biens

Voir les [biens de lien avec la stratégie de remise de biens](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Stratégie de remise de biens DynamicCommonEncryption 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Créer la clé de contenu du type CommonEncryption et liez-le à l’actif

Lorsque vous spécifiez DynamicCommonEncryption remise stratégie, vous devez veillez à lier vos biens à une clé de contenu du type CommonEncryption. Pour plus d’informations, voir : [Création d’une clé de contenu](media-services-rest-create-contentkey.md)).


###<a name="get-delivery-url"></a>Obtenir l’URL de remise

Obtenir l’URL de remise pour le mode de remise PlayReady de la clé de contenu créé à l’étape précédente. Un client utilise l’URL retournée pour demander une licence PlayReady dans l’ordre de lecture du contenu protégé. Pour plus d’informations, consultez [Obtenir l’URL de remise](#get_delivery_url).

###<a name="create-asset-delivery-policy"></a>Créer la stratégie de remise de biens

La requête HTTP suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le chiffrement courantes dynamique (**DynamicCommonEncryption**) pour le protocole **Smooth Streaming** (dans cet exemple, autres protocoles seront bloquées à partir de la diffusion en continu). 

Pour plus d’informations sur les valeurs que vous pouvez spécifier lorsque vous créez un AssetDeliveryPolicy, consultez la section [Types utilisés lorsque vous définissez AssetDeliveryPolicy](#types) .   


Demande :

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Si vous voulez protéger votre contenu à l’aide de Widevine DRM, mettre à jour les valeurs AssetDeliveryConfiguration pour utiliser WidevineLicenseAcquisitionUrl (qui a la valeur 7) et spécifiez l’URL d’un service de remise de licence. Vous pouvez utiliser les partenaires AMS suivants pour vous aider à effectuer des licences Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Par exemple : 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Lors du chiffrement avec Widevine, vous pourrez effectuer à l’aide de tiret. Vérifiez que vous spécifiez tiret (2) dans le protocole de livraison actif.
  
###<a name="link-asset-with-asset-delivery-policy"></a>Bien lien avec la stratégie de remise de biens

Voir les [biens de lien avec la stratégie de remise de biens](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Types d’utilisés lorsque vous définissez AssetDeliveryPolicy

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

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

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

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

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

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
