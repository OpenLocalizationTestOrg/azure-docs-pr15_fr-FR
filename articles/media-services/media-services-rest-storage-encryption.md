<properties 
    pageTitle="Chiffrer votre contenu avec chiffrement de stockage à l’aide de l’API REST AMS" 
    description="Découvrez comment chiffrer votre contenu avec chiffrement de stockage à l’aide des API REST de AMS." 
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


#<a name="encrypting-your-content-with-storage-encryption-using-ams-rest-api"></a>Chiffrer votre contenu avec chiffrement de stockage à l’aide de l’API REST AMS

Il est vivement recommandé pour chiffrer votre contenu localement en utilisant le chiffrement AES 256 bits et puis téléchargez-le sur le stockage Azure l’emplacement de stockage chiffrées au reste.

Cet article donne un aperçu de chiffrement de stockage AMS et vous montre comment télécharger le contenu de stockage chiffré :

- Créer une clé de contenu.
- Créer un bien. Définissez la AssetCreationOption sur StorageEncryption lors de la création du bien.

     Actifs chiffrés doivent être associées à des clés de contenu.
- Lier la clé de contenu de la ressource.  
- Définir le chiffrement des paramètres sur les entités AssetFile.
 
>[AZURE.NOTE]Si vous voulez effectuer une gestion du stockage chiffré, vous devez configurer stratégie de remise de l’actif. Avant de vos biens peuvent être diffusés en continu, le serveur de diffusion supprime le chiffrement de stockage et flux de données votre contenu à l’aide de la stratégie de livraison spécifiée. Pour plus d’informations, voir [Configuration des stratégies de remise actif](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services de support, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez entités dans Media Services, vous devez définir en-tête spécifique champs et des valeurs dans vos requêtes HTTP. Pour plus d’informations, voir [le programme d’installation pour le développement d’API REST Media Services](media-services-rest-how-to-use.md).

>Une fois connecté avec succès à https://media.windows.net, vous recevrez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI comme décrit dans la [connexion à Media Services à l’aide de l’API REST](media-services-rest-connect-programmatically.md). 

##<a name="storage-encryption-overview"></a>Vue d’ensemble du chiffrement de stockage 

Le chiffrement de stockage AMS s’applique le chiffrement **AES-clic** mode à l’intégralité du fichier.  Le mode AES-clic est un chiffrement de blocs qui peut chiffrer les données de longueur arbitraire sans avoir besoin de remplissage. Il fonctionne en chiffrant un bloc compteur avec l’algorithme AES, puis OUX binaire la sortie de AES avec les données pour chiffrer ou déchiffrer.  Bloc de compteur utilisé est construit en copiant la valeur de la InitializationVector à octets 0 à 7 de la valeur du compteur et octets 8 à 15 de la valeur du compteur sont remis à zéro. Du bloc compteur 16 octets, octets 8 à 15 (c'est-à-dire les moins significatifs octets) sont utilisés comme un entier non signé 64 bits simple qui est incrémenté pour chaque bloc suivant de données traitées et est conservé dans l’ordre d’octet réseau. Notez que si ce nombre entier atteint la valeur maximale (0xFFFFFFFFFFFFFFFF) incrémentant réinitialisations du compteur de bloc à zéro (octets 8 à 15) sans affecter les autres 64 bits du compteur (c'est-à-dire 0 à 7 octets).   Pour maintenir la sécurité du chiffrement AES-clic mode, la valeur InitializationVector pour un identificateur de clé donné pour chaque clé contenu doit être unique pour chaque fichier et fichiers doivent être inférieure à 2 ^ 64 blocs longueur.  Il s’agit pour vous assurer que la valeur d’un compteur n’est jamais réutilisée avec une clé donnée. Pour plus d’informations sur le mode clic, consultez [cette page wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (l’article wiki utilise le terme « Valeur unique » au lieu de « InitializationVector »).

Utilisation **Du chiffrement stockage** pour chiffrer votre contenu effacer localement en utilisant le chiffrement AES 256 bits et puis téléchargez-le sur le stockage Azure où il est stocké chiffrées au reste. Ressources protégés par le chiffrement de stockage sont automatiquement non chiffrés et placés dans un système de fichiers chiffrés avant le codage et éventuellement chiffrés avant de le télécharger comme un nouvel actif de sortie. Le cas d’utilisation principal pour le chiffrement de stockage est lorsque vous voulez protéger vos fichiers de média d’entrée de haute qualité avec chiffrement fort inactives sur disque.

Afin de fournir une gestion du stockage chiffré, vous devez configurer stratégie de remise de biens afin que Media Services sache comment vous voulez présenter votre contenu. Avant de vos biens peuvent être diffusés en continu, le serveur de diffusion supprime le chiffrement de stockage et flux de données votre contenu à l’aide de la stratégie de livraison spécifiée (par exemple, AES, chiffrement courantes ou aucun chiffrement).

##<a name="create-contentkeys-used-for-encryption"></a>Créer ContentKeys utilisé pour le chiffrement

Éléments chiffrés doivent être associées à des clé de chiffrement de stockage. Vous devez créer la clé de contenu à utiliser pour le chiffrement avant de créer les fichiers de biens. Cette section décrit comment créer une clé de contenu.

Voici les étapes générales pour générer des clés du contenu que vous devez associer des éléments que vous souhaitez être chiffré. 

1. Pour le chiffrement de stockage, aléatoirement générer une clé AES 32 octets. 

    Il s’agit de la clé de contenu pour vos ressources, ce qui signifie que tous les fichiers associés liés cette ressource doit utiliser la même clé contenue pendant le déchiffrement. 
2.  Appelez les méthodes [GetProtectionKeyId](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkeyid) et [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) pour obtenir le certificat X.509 approprié qui doit être utilisé pour chiffrer votre clé de contenu.
3.  Chiffrer votre clé de contenu avec la clé publique du certificat X.509. 

    Media Services .NET SDK utilise RSA avec OAEP lorsque vous effectuez le chiffrement.  Vous pouvez voir un exemple .NET dans la [fonction EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4.  Créer une valeur de total de contrôle calculée à l’aide de l’identificateur de clé et la clé de contenu. L’exemple .NET suivant calcule la somme de contrôle à l’aide de la partie GUID de l’identificateur de clé et la clé désactivez contenue.
    

        public static string CalculateChecksum(byte[] contentKey, Guid keyId)
        {
            const int ChecksumLength = 8;
            const int KeyIdLength = 16;

            byte[] encryptedKeyId = null;

            // Checksum is computed by AES-ECB encrypting the KID
            // with the content key.
            using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
            {
                rijndael.Mode = CipherMode.ECB;
                rijndael.Key = contentKey;
                rijndael.Padding = PaddingMode.None;

                ICryptoTransform encryptor = rijndael.CreateEncryptor();
                encryptedKeyId = new byte[KeyIdLength];
                encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
            }

            byte[] retVal = new byte[ChecksumLength];
            Array.Copy(encryptedKeyId, retVal, ChecksumLength);

            return Convert.ToBase64String(retVal);
        }


5. Créer la clé de contenu avec **EncryptedContentKey** (converti en chaîne codé en base 64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**et les valeurs de **total de contrôle** que vous avez reçu des étapes précédentes.

    
    Pour le chiffrement de stockage, les propriétés suivantes doivent être incluses dans le corps de la requête.
     
    Propriété corps demande   | Description
    ---|---
    ID | Le ContentKey Id qui nous nous générer au format suivant, « nb:kid:UUID :<NEW GUID>».
    ContentKeyType | Il s’agit du type de contenu clé sous forme d’entier pour cette clé de contenu. Nous transmettre la valeur 1 pour le chiffrement de stockage.
    EncryptedContentKey | Nous créer une nouvelle valeur de clé contenue qui est une valeur de 256 bits (32 octets). La clé est chiffrée à l’aide du certificat X.509 de chiffrement de stockage que nous récupérer à partir de Microsoft Azure Media Services par l’exécution d’une demande HTTP GET pour les GetProtectionKeyId et les méthodes de GetProtectionKey. Par exemple, voir le code .NET suivant : la méthode **EncryptSymmetricKeyData** définie [ici](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Il s’agit de l’id de clés de protection pour le certificat X.509 chiffrement stockage qui a été utilisé pour chiffrer notre clé de contenu.
    ProtectionKeyType | Il s’agit du type de chiffrement pour la clé de protection qui a été utilisé pour chiffrer la clé de contenu. Cette valeur est StorageEncryption(1) dans notre exemple.
    Somme de contrôle |Le MD5 calculé total de contrôle pour la clé de contenu. Elle est calculée par chiffrer le contenu Id avec la clé de contenu. L’exemple de code montre comment calculer le total de contrôle.
    

###<a name="retrieve-the-protectionkeyid"></a>Récupérer la ProtectionKeyId 
 

L’exemple suivant montre comment récupérer la ProtectionKeyId, un empreinte numérique du certificat, pour le certificat que vous devez utiliser pour chiffrer votre clé de contenu. Procédez comme suit pour vous assurer que vous avez déjà le certificat approprié sur votre ordinateur.


Demande :
    
    
    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    

Réponse :
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

###<a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Récupérer la ProtectionKey pour la ProtectionKeyId

L’exemple suivant montre comment récupérer le certificat X.509 à l’aide de la ProtectionKeyId que vous avez reçu dans l’étape précédente.

Demande :
        
    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net
    


Réponse :
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Créer la clé de contenu 

Après avoir récupéré le certificat X.509 et utilisé sa clé publique pour chiffrer votre clé de contenu, créer une entité **ContentKey** et définir les valeurs de ses propriétés en conséquence.

Parmi les valeurs que vous devez définir quand créer le contenu de la clé est le type. En cas du chiffrement de stockage, la valeur est « 1 ». 

L’exemple suivant montre comment créer un **ContentKey** avec un ensemble **ContentKeyType** pour le chiffrement de stockage (« 1 ») et le **ProtectionKeyType** défini sur « 0 » pour indiquer que la clé de protection Id est l’empreinte de certificat X.509.  


Demande

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }


Réponse :
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Créer un bien

L’exemple suivant montre comment créer un bien.

**Demande HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"BigBuckBunny" "Options":1}


**Réponse HTTP**

En cas de réussite, les éléments suivants sont retournés :
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
##<a name="associate-the-contentkey-with-an-asset"></a>Associer le ContentKey un bien

Après avoir créé le ContentKey, associer à vos biens à l’aide de l’opération $links, comme illustré dans l’exemple suivant :
    
Demande :
    
    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Réponse :

    HTTP/1.1 204 No Content 

##<a name="create-an-assetfile"></a>Créer un AssetFile

L’entité [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier vidéo ou audio qui est stocké dans un conteneur blob. Un fichier de biens est toujours associé à un bien, et un bien peut contenir un ou plusieurs fichiers de biens. La tâche Media Services Encoder échoue si un objet de fichier actif n’est pas associé à un fichier dans un conteneur blob numérique.

Notez que l’instance **AssetFile** et le fichier multimédia réel sont deux objets distincts. L’instance AssetFile contient des métadonnées sur le fichier multimédia, tandis que le fichier multimédia contient le contenu du média réel.

Une fois que vous téléchargez votre fichier multimédia dans un conteneur blob, vous allez utiliser la requête HTTP **Fusionner** pour mettre à jour la AssetFile avec les informations relatives à votre fichier multimédia (non affichée dans cette rubrique). 

**Demande HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }

**Réponse HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
