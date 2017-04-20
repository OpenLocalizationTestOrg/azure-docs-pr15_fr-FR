<properties 
    pageTitle="Télécharger des fichiers dans un compte Media Services à l’aide de reste | Microsoft Azure" 
    description="Découvrez comment intégrer du contenu multimédia à Media Services en créant et en téléchargement actifs." 
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="upload-files-into-a-media-services-account-using-rest"></a>Télécharger des fichiers dans un compte Media Services à l’aide de repos

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTE](media-services-rest-upload-files.md)
 - [Portail](media-services-portal-upload-files.md)

Dans les Services de support, vous téléchargez vos fichiers numériques dans un bien. L’entité de [biens](https://msdn.microsoft.com/library/azure/hh974277.aspx) peut contenir vidéo, audio, images, collections miniatures, texte pistes et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés dans l’actif, votre contenu est stocké en toute sécurité dans le nuage pour un traitement ultérieur et la diffusion en continu. 

>[AZURE.NOTE]Les considérations suivantes s’appliquent lors du choix d’un nom de fichier actif :
>
>- Media Services utilise la valeur de la propriété IAssetFile.Name lors de la création d’URL pour le contenu de la diffusion en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Pour cette raison, encodage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères pour cent-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il peut uniquement être une «. » pour l’extension de nom de fichier.
>
>- La longueur du nom ne doit pas dépasser 260 caractères.

Le flux de travail pour le téléchargement des éléments est réparti dans les sections suivantes :

- Créer un bien
- Chiffrer un bien (facultatif)
- Télécharger un fichier sur le stockage d’objets blob

AMS vous permet également de télécharger des éléments en bloc. Pour plus d’informations, consultez [cette](media-services-rest-upload-files.md#upload_in_bulk) section.

##<a name="upload-assets"></a>Télécharger des biens

###<a name="create-an-asset"></a>Créer un bien

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services de support, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez entités dans Media Services, vous devez définir en-tête spécifique champs et des valeurs dans vos requêtes HTTP. Pour plus d’informations, voir [le programme d’installation pour le développement d’API REST Media Services](media-services-rest-how-to-use.md).

>Une fois connecté avec succès à https://media.windows.net, vous recevrez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI comme décrit dans la [connexion à Media Services à l’aide de l’API REST](media-services-rest-connect-programmatically.md). 
 
Une ressource est un conteneur pour plusieurs types d’ou des groupes d’objets dans Media Services, y compris la vidéo, audio, images, des collections de miniatures, légendes et sous-titres fichiers. Dans l’API REST, la création d’un bien nécessite envoi requête POST à Media Services et en plaçant les informations sur vos biens les propriétés dans le corps de la requête.

Un des propriétés que vous pouvez spécifier lors de la création d’un bien est **Options**. **Options** est une valeur d’énumération qui décrit les options de chiffrement un bien pouvant être créées avec. Une valeur valide est une des valeurs dans la liste ci-dessous, pas d’une combinaison de valeurs. 

- **Aucun** = **0**: aucun chiffrement ne sera utilisé. Il s’agit de la valeur par défaut. Notez que lorsque vous utilisez cette option votre contenu n’est pas protégé lors des transferts ou au reste dans le stockage.
    Si vous envisagez d’effectuer un MP4 à l’aide de téléchargement progressif, utilisez cette option. 

- **StorageEncrypted** = **1**: spécifiez si vous souhaitez pour vos fichiers à être chiffrés avec AES 256 bits chiffrement pour le stockage et le téléchargement.

    Si votre actif est stockage chiffré, vous devez configurer stratégie de remise de biens. Pour plus d’informations, voir [stratégie de remise de biens de configuration](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: spécifiez si vous téléchargez des fichiers protégés par une méthode de chiffrement commune (par exemple, PlayReady). 

- **EnvelopeEncryptionProtected** = **4**: spécifiez si vous téléchargez TLS chiffré avec les fichiers AES. Notez que les fichiers doivent avoir été codés et chiffrés par gestionnaire transformer.

>[AZURE.NOTE]Si votre bien utilise le chiffrement, vous devez créer un **ContentKey** et liez-le à vos ressources, comme décrit dans la rubrique suivante :[comment créer un ContentKey](media-services-rest-create-contentkey.md). Notez qu’après le téléchargement de fichiers dans l’actif, vous devez mettre à jour les propriétés de chiffrement sur l’entité **AssetFile** avec les valeurs que vous avez pendant le chiffrement **actif** . Faire à l’aide de la requête HTTP **Fusionner** . 


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
    
    {"Name":"BigBuckBunny.mp4"}
    

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
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
###<a name="create-an-assetfile"></a>Créer un AssetFile

L’entité [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier vidéo ou audio qui est stocké dans un conteneur blob. Un fichier de biens est toujours associé à un bien, et un bien peut contenir un ou plusieurs fichiers de biens. La tâche Media Services Encoder échoue si un objet de fichier actif n’est pas associé à un fichier dans un conteneur blob numérique.

Notez que l’instance **AssetFile** et le fichier multimédia réel sont deux objets distincts. L’instance AssetFile contient des métadonnées sur le fichier multimédia, tandis que le fichier multimédia contient le contenu du média réel.

Une fois que vous téléchargez votre fichier multimédia dans un conteneur blob, vous allez utiliser la requête HTTP **Fusionner** pour mettre à jour la AssetFile avec les informations relatives à votre fichier multimédia (comme illustré plus loin dans la rubrique). 

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
       "IsEncrypted":"false",
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
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Création de la AccessPolicy avec autorisation d’écriture. 

Avant de télécharger les fichiers dans le stockage blob, définir le niveau d’accès des droits de stratégie pour l’écriture dans un bien. Pour ce faire, publiez une demande HTTP pour le jeu d’entités AccessPolicies. Définir une valeur DurationInMinutes lors de la création ou vous recevez un message d’erreur serveur interne 500 en réponse. Pour plus d’informations sur AccessPolicies, voir [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

L’exemple suivant montre comment créer un AccessPolicy :
        
**Demande HTTP**

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Demande HTTP**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

###<a name="get-the-upload-url"></a>Obtenir l’URL de téléchargement

Pour recevoir l’URL de téléchargement réel, créez un repère associations de sécurité. Repères définissent l’heure de début et le type de point de terminaison de connexion pour les clients qui souhaitent accéder aux fichiers dans un bien. Vous pouvez créer plusieurs entités Locator pour une paire AccessPolicy et bien donnée gérer les différentes demandes des clients et les besoins. Chacun de ces repères utilisez la valeur d’heure de début plus la valeur DurationInMinutes de la AccessPolicy pour déterminer la durée d’une URL peut être utilisée. Pour plus d’informations, voir [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Une URL de sa a le format suivant :

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Certaines considérations s’appliquent :

- Vous ne peut pas contenir plus de cinq Locator unique associé à une ressource donnée en même temps. Pour plus d’informations, voir Locator.
- Si vous avez besoin de télécharger vos fichiers immédiatement, vous devez définir la valeur d’heure de début à cinq minutes avant l’heure actuelle. Il s’agit, car il peut y avoir horloge inclinaison entre votre ordinateur client et Media Services. En outre, votre valeur d’heure de début doit être placé dans le format de date/heure suivant : AAAA-MM-JJThh (par exemple, « 2014-05-23T17:53:50Z »).   
- Il peut y avoir un deuxième 30-40 retarder après avoir créé un repère à lorsqu’elle est disponible pour une utilisation. Ce problème concerne sa URL et Locator d’origine.

L’exemple suivant montre comment créer une adresse URL associations de sécurité, telle que définie par la propriété de Type dans le corps de la demande (« 1 » pour un repère associations de sécurité) et « 2 » pour un repère d’origine à la demande. La propriété de **chemin d’accès** retournée contient l’URL que vous devez utiliser pour télécharger votre fichier.
    
**Demande HTTP**
    
    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Réponse HTTP**

En cas de réussite, la réponse suivante est renvoyée :
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Télécharger un fichier dans un conteneur de stockage blob
    
Une fois que la AccessPolicy et Locator définir, le fichier est téléchargé dans un conteneur de stockage d’objets Blob Azure à l’aide de l’API REST de stockage Azure. Vous pouvez télécharger dans la page ou bloquer des objets BLOB. 

>[AZURE.NOTE] Vous devez ajouter le nom de fichier pour le fichier que vous voulez télécharger à la valeur Locator **chemin d’accès** reçue dans la section précédente. Par exemple, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Pour plus d’informations sur l’utilisation des objets BLOB Azure stockage, voir [L’API REST Blob Service](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Mettre à jour la AssetFile 

À présent que vous avez téléchargé votre fichier, mettez à jour les informations FileAsset taille (et autres). Par exemple :
    
    MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Réponse HTTP**

Si la réussite, ce qui suit est renvoyé : HTTP/1.1 204 aucun contenu

### <a name="delete-the-locator-and-accesspolicy"></a>Supprimer les Locator et AccessPolicy 

**Demande HTTP**


    DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
**Réponse HTTP**

En cas de réussite, les éléments suivants sont retournés :

    HTTP/1.1 204 No Content 
    ...

**Demande HTTP**

    DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

**Réponse HTTP**

En cas de réussite, les éléments suivants sont retournés :

    HTTP/1.1 204 No Content 
    ...

##<a id="upload_in_bulk"></a>Télécharger les éléments en bloc

###<a name="create-the-ingestmanifest"></a>Créer la IngestManifest

Le IngestManifest est un conteneur pour un ensemble de biens, fichiers de biens et informations statistique qui peuvent être utilisées pour déterminer l’avancement de bloc traiter pour l’ensemble.


**Demande HTTP**

    POST https:// media.windows.net/API/IngestManifests HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 36
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST" }

###<a name="create-assets"></a>Créer des biens

Avant de créer la IngestManifestAsset, vous devez créer l’actif sera terminée à l’aide de traiter les en bloc. Une ressource est un conteneur pour plusieurs types d’ou des groupes d’objets dans Media Services, y compris la vidéo, audio, images, des collections de miniatures, légendes et sous-titres fichiers. Dans l’API REST, la création d’un bien nécessite envoyant une demande HTTP POST à Microsoft Azure Media Services et en plaçant les informations sur vos biens les propriétés dans le corps de la requête. Dans cet exemple, l’actif est créé à l’aide de l’option StorageEncrption(1) incluse dans le corps de la requête.

**Réponse HTTP**

    POST https://media.windows.net/API/Assets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 55
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST_Asset", "Options" : 1 }

###<a name="create-the-ingestmanifestassets"></a>Créer la IngestManifestAssets

IngestManifestAssets représentent actifs au sein d’un IngestManifest qui sont utilisés avec traiter les en bloc. Le lien fondamentalement du bien au manifeste. Azure Media Services surveille en interne pour le téléchargement de fichier en fonction de la collection IngestManifestFiles associée à la IngestManifestAsset. Une fois ces fichiers sont téléchargés, l’actif est terminé. Vous pouvez créer un nouveau IngestManifestAsset avec une demande HTTP POST. Dans le corps de la requête, inclure l’IngestManifest Id et l’Id de biens la IngestManifestAsset doit relier pour traiter les en bloc.

**Réponse HTTP**

    POST https://media.windows.net/API/IngestManifestAssets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 152
    Expect: 100-continue
    { "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "Asset" : { "Id" : "nb:cid:UUID:b757929a-5a57-430b-b33e-c05c6cbef02e"}}


###<a name="create-the-ingestmanifestfiles-for-each-asset"></a>Créer la IngestManifestFiles pour chaque ressource

Un IngestManifestFile représente un objet réel blob vidéo ou audio qui est téléchargé dans le cadre de traiter les en bloc pour une ressource. Chiffrement liés propriétés ne sont pas obligatoires, à moins que l’actif utilise une option de chiffrement. L’exemple utilisé dans cette section illustre la création d’un IngestManifestFile qui utilise StorageEncryption pour l’actif créé précédemment.


**Réponse HTTP**

    POST https://media.windows.net/API/IngestManifestFiles HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 367
    Expect: 100-continue
    
    { "Name" : "REST_Example_File.wmv", "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "ParentIngestManifestAssetId" : "nb:maid:UUID:beed8531-9a03-9043-b1d8-6a6d1044cdda", "IsEncrypted" : "true", "EncryptionScheme" : "StorageEncryption", "EncryptionVersion" : "1.0", "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510" }
    
###<a name="upload-the-files-to-blob-storage"></a>Téléchargez les fichiers au stockage d’objets Blob

Vous pouvez utiliser n’importe quelle application de client haut débit capable de télécharger les fichiers de biens pour le conteneur de stockage blob Uri fournie par la propriété BlobStorageUriForUpload de la IngestManifest. Un service de téléchargement remarquables haut débit est [Aspera sur la demande d’Application Azure](http://go.microsoft.com/fwlink/?LinkId=272001).

###<a name="monitor-bulk-ingest-progress"></a>L’avancement d’acquisition moniteur en bloc

Vous pouvez surveiller l’avancement de bloc traiter les opérations pour une IngestManifest par l’interrogation de la propriété statistiques de la IngestManifest. Cette propriété est un type complexe, [IngestManifestStatistics](https://msdn.microsoft.com/library/azure/jj853027.aspx). Pour interroger la propriété statistiques, envoyez une demande HTTP GET passant la IngestManifest Id.
 

##<a name="create-contentkeys-used-for-encryption"></a>Créer ContentKeys utilisé pour le chiffrement

Si votre bien utilise le chiffrement, vous devez créer la ContentKey à utiliser pour le chiffrement avant de créer les fichiers de biens. Pour le chiffrement de stockage, les propriétés suivantes doivent être incluses dans le corps de la requête.
 
Propriété corps demande   | Description
---|---
ID | Le ContentKey Id qui nous nous générer au format suivant, « nb:kid:UUID :<NEW GUID>».
ContentKeyType | Il s’agit du type de contenu clé sous forme d’entier pour cette clé de contenu. Nous transmettre la valeur 1 pour le chiffrement de stockage.
EncryptedContentKey | Nous créer une nouvelle valeur de clé contenue qui est une valeur de 256 bits (32 octets). La clé est chiffrée à l’aide du certificat X.509 de chiffrement de stockage que nous récupérer à partir de Microsoft Azure Media Services par l’exécution d’une demande HTTP GET pour les GetProtectionKeyId et les méthodes de GetProtectionKey.
ProtectionKeyId | Il s’agit de l’id de clés de protection pour le certificat X.509 chiffrement stockage qui a été utilisé pour chiffrer notre clé de contenu.
ProtectionKeyType | Il s’agit du type de chiffrement pour la clé de protection qui a été utilisé pour chiffrer la clé de contenu. Cette valeur est StorageEncryption(1) dans notre exemple.
Somme de contrôle |Le MD5 calculé total de contrôle pour la clé de contenu. Elle est calculée par chiffrer le contenu Id avec la clé de contenu. L’exemple de code montre comment calculer le total de contrôle.


**Réponse HTTP**
    
    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 572
    Expect: 100-continue
    
    {"Id" : "nb:kid:UUID:316d14d4-b603-4d90-b8db-0fede8aa48f8", "ContentKeyType" : 1, "EncryptedContentKey" : "Y4NPej7heOFa2vsd8ZEOcjjpu/qOq3RJ6GRfxa8CCwtAM83d6J2mKOeQFUmMyVXUSsBCCOdufmieTKi+hOUtNAbyNM4lY4AXI537b9GaY8oSeje0NGU8+QCOuf7jGdRac5B9uIk7WwD76RAJnqyep6U/OdvQV4RLvvZ9w7nO4bY8RHaUaLxC2u4aIRRaZtLu5rm8GKBPy87OzQVXNgnLM01I8s3Z4wJ3i7jXqkknDy4VkIyLBSQvIvUzxYHeNdMVWDmS+jPN9ScVmolUwGzH1A23td8UWFHOjTjXHLjNm5Yq+7MIOoaxeMlKPYXRFKofRY8Qh5o5tqvycSAJ9KUqfg==", "ProtectionKeyId" : "7D9BB04D9D0A4A24800CADBFEF232689E048F69C", "ProtectionKeyType" : 1, "Checksum" : "TfXtjCIlq1Y=" }

### <a name="link-the-contentkey-to-the-asset"></a>Atteindre la ContentKey du bien

Le ContentKey est associé à un ou plusieurs éléments en envoyant une demande HTTP POST. La requête suivante est un exemple pour lier l’exemple ContentKey à l’actif exemple en identifiant.

**Réponse HTTP**
    
    POST https://media.windows.net/API/Assets('nb:cid:UUID:b3023475-09b4-4647-9d6d-6fc242822e68')/$links/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 113
    Expect: 100-continue
    
    { "uri": "https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A32e6efaf-5fba-4538-b115-9d1cefe43510')"}

**Réponse HTTP**

    GET https://media.windows.net/API/IngestManifests('nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net



##<a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



 
[How to Get a Media Processor]: media-services-get-media-processor.md
 
