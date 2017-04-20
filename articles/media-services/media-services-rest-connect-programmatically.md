<properties 
    pageTitle="Connexion au compte de Services de support à l’aide de l’API REST | Microsoft Azure" 
    description="Cette rubrique montre comment se connecter à Media Services en utilisant l’API REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
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


# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>Connexion au compte de Services de support à l’aide de l’API REST de Services de support

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [RESTE](media-services-rest-connect-programmatically.md)

Cette rubrique explique comment obtenir une connexion à Microsoft Azure Media Services par programme lors de la programmation avec l’API REST de Services de support.

Deux éléments sont requises lorsque vous accédez à Microsoft Azure Media Services : un jeton d’accès fourni par les Services de contrôle d’accès Azure (ACS) et URI de Media Services lui-même. Vous pouvez utiliser tout moyen à lors de la création de ces demandes tant que vous spécifiez les valeurs correctes en-tête et de passez correctement dans le jeton d’accès lors de l’appel dans Media Services.

Les étapes suivantes décrivent le flux de travail plus courantes lors de l’utilisation de l’API REST de Services de support pour vous connecter aux Services de support :

1. Obtenir un jeton d’accès 
2. Se connecter aux Services de support URI 

    >[AZURE.NOTE] Une fois connecté avec succès à https://media.windows.net, vous recevrez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI.
Vous pouvez également recevoir une réponse HTTP/1.1 200 qui contient la description des métadonnées ODATA API.

3. Publier vos appels API suivants vers la nouvelle URL. 

    Par exemple, si, après avoir essayé de vous connecter, vous avez obtenu les éléments suivants :

        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Vous devez enregistrer vos appels API suivants à https://wamsbayclus001rest-hs.cloudapp.net/api/.

##<a name="access-control-address"></a>Adresse de contrôle d’accès

Adresse de contrôle d’accès Media Services est https://wamsprodglobal001acs.accesscontrol.windows.net, à l’exception de la région en Chine du Nord où il est https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##<a name="getting-an-access-token"></a>Obtenir un jeton d’accès

Pour accéder aux Services de support directement par le biais de l’API REST, extraire un jeton d’accès ACS et utilisez-le lors de chaque demande HTTP que transformer le service. Ce jeton est similaire à d’autres jetons fournis par ACS basée sur les revendications access fournies dans l’en-tête d’une demande HTTP et en utilisant le protocole v2 OAuth. Vous n’avez pas besoin de toute autre condition requise avant de vous connecter directement aux Services de support.

L’exemple suivant montre l’en-tête de demande HTTP et le corps utilisée pour récupérer un jeton.

**En-tête**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**Corps**:

Vous avez besoin prouver les valeurs identifiant_client et client_secret dans le corps de cette requête ; identifiant_client et client_secret correspondent aux valeurs nom de compte et AccountKey, respectivement. Ces valeurs sont fournies pour vous par Media Services lorsque vous configurez votre compte. 

Notez que la AccountKey pour votre compte Media Services doit être codée au format URL (voir [Encodage](http://tools.ietf.org/html/rfc3986#section-2.1) lors de l’utilisation en tant que la valeur client_secret dans votre demande jeton d’accès.

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Par exemple : 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


L’exemple suivant illustre la réponse HTTP qui contient le niveau d’accès jeton dans le corps de réponse.

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Il est recommandé pour mettre en cache les valeurs « access_token » et « expires_in » avec un stockage externe. Les données jetons ultérieurement peuvent être récupérées à partir du stockage et réutilisées dans vos appels de l’API REST Media Services. Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou ordinateurs.

Veillez à contrôler la valeur « expires_in » du jeton d’accès et mettre à jour de vos appels API REST de nouveaux jetons selon vos besoins.

###<a name="connecting-to-the-media-services-uri"></a>Se connecter aux Services de support URI

La racine URI pour Media Services est https://media.windows.net/. Vous devez vous connecter initialement à cet URI, et si vous obtenez une redirection 301 en réponse, vous devez faire d’autres appels vers le nouvel URI. En outre, n’utilisez pas une logique de redirection automatique/suivre dans vos demandes. Verbes HTTP et corps de requête ne sont pas transférés vers le nouvel URI.

Notez que la racine URI pour le téléchargement des fichiers de biens est https://yourstorageaccount.blob.core.windows.net/ où le nom de compte de stockage est identique à celle que vous utilisée pendant l’installation de votre compte Media Services.

L’exemple suivant illustre la requête HTTP à la racine de Media Services URI (https://media.windows.net/). La demande Obtient une redirection 301 en réponse. La requête suivante est à l’aide du nouvel URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Demande HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**Réponse HTTP**:
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**Demande HTTP** (à l’aide du nouvel URI) :
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**Réponse HTTP**:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] Une fois que vous obtenez le nouvel URI, qui est l’URI qui doit être utilisé pour communiquer avec les Services de support. 


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
