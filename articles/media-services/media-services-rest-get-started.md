<properties 
    pageTitle="Prise en main diffuser du contenu à la demande à l’aide de reste | Microsoft Azure" 
    description="Ce didacticiel vous guide dans les étapes de l’implémentation d’une application de distribution de contenu à la demande sur avec Azure Media Services à l’aide de l’API REST." 
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
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>Prise en main diffuser du contenu à la demande à l’aide de repos 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](/pricing/free-trial/?WT.mc_id=A261C142F). 


Ce démarrage rapide vous guide dans les étapes de l’implémentation d’une application de distribution de contenu vidéo à la demande (VoD) à l’aide des API REST de Azure Media Services (AMS). 

Le didacticiel présente le flux de travail Media Services et les objets de programmation courantes et les tâches requis pour le développement de Media Services. À la fin du didacticiel, vous pourrez flux ou progressivement télécharger un exemple de fichier multimédia que vous avez téléchargée, codé et téléchargé.  

## <a name="prerequisites"></a>Conditions préalables
Les composants requis suivants sont requis pour commencer à développer avec Media Services avec l’API REST.

- Comprendre comment développer avec l’API REST Media Services. Pour plus d’informations, voir [présentation multimédia-services-reste](http://msdn.microsoft.com/library/azure/hh973616.aspx).
- L’application de votre choix qui peut envoyer des demandes HTTP et les réponses. Ce didacticiel utilise [Fiddler](http://www.telerik.com/download/fiddler). 

Les tâches suivantes sont représentées dans ce démarrage rapide.

1.  Créer un compte Media Services (à l’aide du portail Azure).
2.  Configurer le point de terminaison de diffusion en continu (à l’aide du portail Azure).
1.  Connectez-vous au compte Media Services avec l’API REST.
1.  Créer un nouvel actif et télécharger un fichier vidéo avec l’API REST.
1.  Configurer les unités de diffusion en continu avec l’API REST.
2.  Encoder le fichier source dans un jeu de fichiers MP4 de débit adaptatif avec l’API REST.
1.  Publier les biens et les get diffusion en continu et les URL de téléchargement progressive avec l’API REST. 
1.  Lire votre contenu. 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Créer un compte Azure Media Services à l’aide du portail Azure

Les étapes décrites dans cette section montrent comment créer un compte AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Media + CDN** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** Entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte Media Services est tous les nombres en minuscules ou des lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure que vous avez accès.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. En savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans un **emplacement**, sélectionnez la région géographique est utilisé pour stocker les enregistrements de métadonnées et éléments multimédias de votre compte Media Services. Cette zone est utilisée pour traiter et diffuser vos médias. Uniquement les zones des Services de support disponibles apparaissent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique comme votre compte Media Services, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles de noms de compte de stockage sont les mêmes que pour les comptes Media Services.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour afficher la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, Encoder actifs, surveiller l’avancement de tâche) utilisez la fenêtre **paramètres** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurer les points de terminaison de diffusion en continu à l’aide du portail Azure

Lorsque vous travaillez avec Azure Media Services un des scénarios plus courants est offrant des performances vidéo via adaptive débit diffusion en continu à vos clients. Media Services prend en charge le débit adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Media Services fournit emballage dynamique qui vous permet d’effectuer votre débit adaptive contenu MP4 codé en continu les formats pris en charge par Media Services (MPEG tiret, TLS, Smooth Streaming, HDS) juste-à-temps, sans avoir à stocker des versions prédéfinies de chacun de ces formats de diffusion en continu.

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier mezzanine (source) dans un jeu de fichiers de débit adaptive MP4 (les étapes de codage vous trouverez plus loin dans ce didacticiel).  
- Créer au moins une unité de diffusion en continu pour le *point de terminaison de la diffusion* à partir de laquelle vous envisagez de remise votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de diffusion en continu.

Avec emballage dynamique, vous ne devez stocker et payer pour les fichiers au format de stockage unique et Media Services crée et gère la réponse appropriée basée sur les requêtes à partir d’un client.

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :


1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison de la diffusion**. 

2. Cliquez sur la valeur par défaut diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de diffusion en continu, faites glisser le curseur **unités de diffusion en continu** .

    ![Unités de diffusion en continu](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de n’importe quelle unité nouveau peut prendre jusqu'à 20 minutes.

## <a id="connect"></a>Connectez-vous au compte Media Services avec l’API REST

Deux éléments sont requises lorsque vous accédez à Azure Media Services : un jeton d’accès fourni par les Services de contrôle d’accès Azure (ACS) et URI de Media Services lui-même. Vous pouvez utiliser tout moyen à lors de la création de ces demandes tant que vous spécifiez les valeurs correctes en-tête et de passez correctement dans le jeton d’accès lors de l’appel dans Media Services.

Les étapes suivantes décrivent le flux de travail plus courantes lors de l’utilisation de l’API REST de Services de support pour vous connecter aux Services de support :

1. Obtenir un jeton d’accès. 
2. Se connecter aux Services de support URI.  

    N’oubliez pas qu’une fois connecté avec succès à https://media.windows.net, vous recevez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI. Vous pouvez également recevoir une réponse HTTP/1.1 200 qui contient la description des métadonnées ODATA API.
3. En publiant vos appels API suivants vers la nouvelle URL. 
    
    Par exemple, si, après avoir essayé de vous connecter, vous avez obtenu les éléments suivants :
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Vous devez enregistrer vos appels API suivants à https://wamsbayclus001rest-hs.cloudapp.net/api/.

###<a name="getting-an-access-token"></a>Obtenir un jeton d’accès

Pour accéder aux Services de support directement par le biais de l’API REST, extraire un jeton d’accès ACS et utilisez-le lors de chaque demande HTTP que transformer le service. Vous n’avez pas besoin de toute autre condition requise avant de vous connecter directement aux Services de support.

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

Vous devez avère les valeurs identifiant_client et client_secret dans le corps de cette requête ; identifiant_client et client_secret correspondent aux valeurs nom de compte et AccountKey, respectivement. Ces valeurs sont fournies pour vous par Media Services lorsque vous configurez votre compte. 

La AccountKey pour votre compte Media Services doit être codée au format URL lorsque vous utilisez comme valeur client_secret dans votre demande jeton d’accès.

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
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Il est recommandé pour mettre en cache les valeurs « access_token » et « expires_in » avec un stockage externe. Les données jetons ultérieurement peuvent être récupérées à partir du stockage et réutilisées dans vos appels de l’API REST Media Services. Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou ordinateurs.

Veillez à contrôler la valeur « expires_in » du jeton d’accès et mettre à jour de vos appels API REST de nouveaux jetons selon vos besoins.

###<a name="connecting-to-the-media-services-uri"></a>Se connecter aux Services de support URI

La racine URI pour Media Services est https://media.windows.net/. Vous devez vous connecter initialement à cet URI, et si vous obtenez une redirection 301 en réponse, vous devez faire d’autres appels vers le nouvel URI. En outre, n’utilisez pas une logique de redirection automatique/suivre dans vos demandes. Verbes HTTP et corps de requête ne sont pas transférés vers le nouvel URI.

La racine URI pour le téléchargement des fichiers de biens est https://yourstorageaccount.blob.core.windows.net/ où le nom de compte de stockage est identique à celle que vous utilisée pendant l’installation de votre compte Media Services.

L’exemple suivant illustre la requête HTTP à la racine de Media Services URI (https://media.windows.net/). La demande Obtient une redirection 301 en réponse. La requête suivante est à l’aide du nouvel URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Demande HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
     


>[AZURE.NOTE] Par la suite du nouvel URI est utilisé dans ce didacticiel.

## <a id="upload"></a>Créer un nouvel actif et télécharger un fichier vidéo avec l’API REST

Dans les Services de support, vous téléchargez vos fichiers numériques dans un bien. L’entité de **biens** peut contenir vidéo, audio, images, collections miniatures, texte pistes et sous-titres fichiers (et les métadonnées relatives à ces fichiers.)  Une fois que les fichiers sont téléchargés dans l’actif, votre contenu est stocké en toute sécurité dans le nuage pour un traitement ultérieur et la diffusion en continu. 

Un des valeurs que vous devez fournir lors de la création d’un bien est options de création de biens. La propriété **Options** est une valeur d’énumération qui décrit les options de chiffrement un bien pouvant être créées avec. Une valeur valide est une des valeurs dans la liste ci-dessous, pas d’une combinaison de valeurs de cette liste :

 
- **Aucun** = **0** - aucun chiffrement est utilisé. Lorsque vous utilisez cette option votre contenu n’est pas protégé lors des transferts ou au reste dans le stockage.
    Si vous envisagez d’effectuer un MP4 à l’aide de téléchargement progressif, utilisez cette option. 
- **StorageEncrypted** = **1** - chiffre votre contenu effacer localement en utilisant le chiffrement AES 256 bits, puis la télécharge au stockage Azure où il est stocké chiffrées au reste. Ressources protégés par le chiffrement de stockage sont automatiquement non chiffrés et placés dans un système de fichiers chiffrés avant le codage et éventuellement chiffrés avant de le télécharger comme un nouvel actif de sortie. Le cas d’utilisation principal pour le stockage de chiffrement est lorsque vous voulez protéger vos fichiers multimédias d’entrée de haute qualité avec chiffrement fort inactives sur disque.
- **CommonEncryptionProtected** = **2** : utilisez cette option si vous téléchargez du contenu déjà chiffré et protégé par chiffrement courantes ou PlayReady DRM (par exemple, Smooth Streaming protégé avec PlayReady DRM).
- **EnvelopeEncryptionProtected** = **4** : utilisez cette option si vous téléchargez TLS cryptée avec AES. Les fichiers doivent codés et chiffrées par Gestionnaire de transformation.

### <a name="create-an-asset"></a>Créer un bien

Une ressource est un conteneur pour plusieurs types d’ou des groupes d’objets dans Media Services, y compris la vidéo, audio, images, des collections de miniatures, légendes et sous-titres fichiers. Dans l’API REST, la création d’un bien nécessite envoi requête POST à Media Services et en plaçant les informations sur vos biens les propriétés dans le corps de la requête.

L’exemple suivant montre comment créer un bien.

**Demande HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

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
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>Créer un AssetFile

L’entité [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier vidéo ou audio qui est stocké dans un conteneur blob. Un fichier de biens est toujours associé à un bien, et un bien peut contenir un ou plusieurs AssetFiles. La tâche Media Services Encoder échoue si un objet de fichier actif n’est pas associé à un fichier dans un conteneur blob numérique.

Une fois que vous téléchargez votre fichier multimédia dans un conteneur blob, la requête HTTP **Fusionner** vous permet de mettre à jour la AssetFile avec les informations relatives à votre fichier multimédia (comme illustré plus loin dans la rubrique). 

**Demande HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
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

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Réponse HTTP**

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
    X-Powered-By: ASP.NET
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

### <a name="get-the-upload-url"></a>Obtenir l’URL de téléchargement

Pour recevoir l’URL de téléchargement réel, créez un repère associations de sécurité. Repères définissent l’heure de début et le type de point de terminaison de connexion pour les clients qui souhaitent accéder aux fichiers dans un bien. Vous pouvez créer plusieurs entités Locator pour une paire AccessPolicy et bien donnée gérer les différentes demandes des clients et les besoins. Chacun de ces repères utilise la valeur d’heure de début plus la valeur DurationInMinutes de la AccessPolicy pour déterminer la durée de qu'une URL peut être utilisée. Pour plus d’informations, voir [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Une URL de sa a le format suivant :

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Certaines considérations s’appliquent :

- Vous ne peut pas contenir plus de cinq Locator unique associé à une ressource donnée en même temps. Pour plus d’informations, voir Locator.
- Si vous avez besoin de télécharger vos fichiers immédiatement, vous devez définir la valeur d’heure de début à cinq minutes avant l’heure actuelle. Il s’agit, car il peut y avoir horloge inclinaison entre votre ordinateur client et Media Services. En outre, votre valeur d’heure de début doit être placé dans le format de date/heure suivant : AAAA-MM-JJThh (par exemple, « 2014-05-23T17:53:50Z »).   
- Il peut y avoir un deuxième 30-40 retarder après avoir créé un repère à lorsqu’elle est disponible pour une utilisation. Ce problème concerne sa URL et Locator d’origine.

L’exemple suivant montre comment créer une adresse URL associations de sécurité, telle que définie par la propriété de Type dans le corps de la demande (« 1 » pour un repère associations de sécurité) et « 2 » pour un repère d’origine à la demande. La propriété de **chemin d’accès** retournée contient l’URL que vous devez utiliser pour télécharger votre fichier.
    
**Demande HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
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
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Réponse HTTP**

Si la réussite, ce qui suit est renvoyé : HTTP/1.1 204 aucun contenu

## <a name="delete-the-locator-and-accesspolicy"></a>Supprimer les Locator et AccessPolicy 

**Demande HTTP**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**Réponse HTTP**

En cas de réussite, les éléments suivants sont retournés :

    HTTP/1.1 204 No Content 
    ...

**Demande HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Réponse HTTP**

En cas de réussite, les éléments suivants sont retournés :

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>Configurer des unités de diffusion en continu avec l’API REST

Lorsque vous travaillez avec Azure Media Services un des scénarios plus courants a décidé de débit adaptive diffusion en continu à vos clients. Avec débit adaptive diffusion en continu, le client peut passer à un flux de débit supérieure ou inférieure comme la vidéo est affichée en fonction de la bande passante réseau, de l’UC et d’autres facteurs. Media Services prend en charge le débit adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement). 

Media Services fournit emballage dynamique qui vous permet d’effectuer votre contenu adaptive débit MP4 ou Smooth Streaming codé en continu les formats pris en charge par Media Services (MPEG tiret, TLS, Smooth Streaming, HDS) sans avoir à réorganiser dans ces formats de diffusion en continu. 

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- obtenir au moins une unité de diffusion en continu pour le **point de terminaison de la diffusion **à partir de laquelle vous souhaitez présenter votre contenu (décrite dans cette section).
- coder ou transcoder votre mezzanine (source) de fichiers dans un jeu de débit adaptive MP4 fichiers ou débit adaptive Smooth Streaming (les étapes de codage vous trouverez plus loin dans ce didacticiel),  

Avec emballage dynamique, vous ne devez stocker et payer pour les fichiers au format de stockage unique et Media Services crée et gère la réponse appropriée basée sur les requêtes à partir d’un client. 


>[AZURE.NOTE] Pour plus d’informations sur les tarifs plus d’informations, voir [Media Services tarifs Details](http://go.microsoft.com/fwlink/?LinkId=275107).

Pour modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>Obtenir le point de terminaison de diffusion en continu à mettre à jour

Par exemple, nous allons obtenir le premier point de terminaison de diffusion en continu dans votre compte (vous pouvez avoir jusqu'à deux points de terminaison de diffusion en continu en état d’exécution en même temps).

**Demande HTTP**:

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Réponse HTTP**
    
En cas de réussite, les éléments suivants sont retournés :
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>Échelle de point de terminaison de la diffusion en continu
 
**Demande HTTP**:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**Réponse HTTP**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>Vérifier l’état d’une opération longue

L’allocation de n’importe quelle unité nouveau prend environ 20 minutes. Pour vérifier l’état de l’opération, utilisez la méthode **opérations** et indiquez l’Id de l’opération. L’opération Id a été retourné dans la réponse à la demande **d’échelle** .

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**Demande HTTP**:
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**Réponse HTTP**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>Encoder le fichier source dans un jeu de fichiers MP4 de débit adaptatif

Avant après réception Qu'actifs dans Media Services, media peuvent être codés, transmuxed, filigrane et ainsi de suite, il est envoyée aux clients. Ces activités sont planifiées et exécutez sur plusieurs instances de rôle de l’arrière-plan pour garantir la disponibilité et les performances. Ces activités sont appelées tâches et chaque [tâche](http://msdn.microsoft.com/library/azure/hh974289.aspx) est composée de tâches atomiques faire le travail réel sur le fichier actif. 

Comme mentionné précédemment, lorsque vous travaillez avec Azure Media Services un des scénarios plus courants a décidé de débit adaptive diffusion en continu à vos clients. Media Services peuvent empaqueter dynamiquement un jeu de fichiers de débit adaptive MP4 dans un des formats suivants : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement). 

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- coder ou transcoder votre mezzanine (source) de fichiers dans un jeu de débit adaptive MP4 fichiers ou débit adaptive Smooth Streaming,  
- accéder au moins une unité de diffusion en continu du point de terminaison de diffusion en continu à partir de laquelle vous souhaitez présenter votre contenu. 

La section suivante montre comment créer une tâche qui contient une tâche de codage. La tâche spécifie le fichier mezzanine à transcoder dans un ensemble de débit adaptive MP4s à l’aide de **Media Encoder Standard**. La section indique également comment surveiller le travail de l’avancement de traitement. Lorsque la tâche est terminée, vous ne pourrez pas créer Locator est nécessaires pour accéder à vos ressources. 

### <a name="get-a-media-processor"></a>Obtenir un processeur média

Dans les Services de support, un processeur média est un composant qui gère une tâche de traitement spécifiques, telles que le codage, la conversion du format, chiffrement ou déchiffrement contenu multimédia. Pour la tâche codage présentée dans ce didacticiel, nous allons utiliser la norme de décodage d’éléments multimédia.

Le code suivant les demandes d’id de l’encodeur. 

**Demande HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Réponse HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Créer une tâche

Chaque tâche peut avoir une ou plusieurs tâches selon le type de traitement que vous souhaitez exécuter. À l’API REST, vous pouvez créer des tâches et leurs tâches associées dans de deux façons : les tâches peuvent être définis inline via la propriété de navigation de tâches sur entités tâche ou traitement par lots OData. Le Kit de développement de Services de support utilise le traitement par lots. Toutefois, pour une meilleure lisibilité des exemples de code dans cette rubrique, les tâches sont définies inline. Pour plus d’informations sur le traitement par lots, voir la rubrique [Traitement de lot Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

L’exemple suivant vous montre comment créer et publier un projet avec une que tâche configurée pour coder une vidéo à une résolution spécifique et une qualité. La section suivante de la documentation contient la liste de toutes les [tâches Présélections](http://msdn.microsoft.com/library/mt269960) pris en charge par le processeur Media Encoder Standard.  

**Demande HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Réponse HTTP**

En cas de réussite, la réponse suivante est renvoyée :

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


Il existe quelques points importants à signaler toute demande de tâche :

- Propriétés TaskBody doivent utiliser littéral XML pour définir le nombre d’entrées ou de sortie actifs qui sont utilisés par la tâche. La rubrique tâches contient la définition de schéma XML pour le fichier XML.
- Dans la définition TaskBody, chaque interne valeur pour <inputAsset> et <outputAsset> doit être définie en tant que JobInputAsset(value) ou JobOutputAsset(value).
- Une tâche peut avoir plusieurs éléments de sortie. Un seul JobOutputAsset(x) utilisable uniquement une fois comme résultat d’une tâche dans une tâche.
- Vous pouvez spécifier JobInputAsset ou JobOutputAsset comme un bien d’entrée d’une tâche.
- Tâches ne doivent pas former un cycle.
- Le paramètre de valeur que vous passez à JobInputAsset ou JobOutputAsset représente la valeur d’index pour une ressource. Les biens réels sont définis dans les propriétés de navigation InputMediaAssets et OutputMediaAssets sur la définition de l’entité tâche. 

>[AZURE.NOTE] Étant donné que Media Services est basé sur OData v3, les éléments individuels dans les ensembles de propriétés de navigation InputMediaAssets et OutputMediaAssets sont référencées par une « __metadata : uri « paire nom-valeur. 

- InputMediaAssets correspond à un ou plusieurs éléments que vous avez créé dans Media Services. OutputMediaAssets sont créés par le système. Ils ne font pas référencent à un actif existant.
- OutputMediaAssets peuvent être nommés à l’aide de l’attribut assetName. Si cet attribut n’est pas présent, puis le nom de la OutputMediaAsset est quelle que soit la valeur de texte interne de la <outputAsset> élément est le suffixe la valeur nom de la tâche, ou la valeur de l’Id de la tâche (dans le cas où la propriété nom n’est pas définie). Par exemple, si vous définissez une valeur pour assetName à « Échantillon », la propriété OutputMediaAsset Name être définie à « Échantillon ». Toutefois, si vous n’avez pas défini une valeur pour assetName, mais n’avez défini le nom du travail à « NewJob », le nom OutputMediaAsset serait « _NewJob JobOutputAsset (valeur) ». 

    L’exemple suivant montre comment définir l’attribut assetName :
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- Pour activer la chaîne de la tâche :

    - Un projet doit avoir au moins deux tâches
    - Doit comporter au moins une tâche dont l’entrée est sortie d’une autre tâche dans la tâche.

Pour plus d’informations, voir [Création d’une tâche de codage avec l’API REST de Services de support](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### <a name="monitor-processing-progress"></a>Surveiller l’avancement de traitement

Vous pouvez récupérer l’état du travail à l’aide de la propriété State, comme le montre l’exemple suivant. 

**Demande HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Réponse HTTP**

En cas de réussite, la réponse suivante est renvoyée :

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Annulation d’une tâche

Media Services vous permet d’annuler les travaux en cours d’exécution via la fonction CancelJob. Cet appel renvoie un code d’erreur 400 si vous essayez d’annulation d’une tâche lorsque son état est annulé annulation, erreur, ou vous avez terminé.

L’exemple suivant montre comment appeler CancelJob.


**Demande HTTP**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


En cas de réussite, un code de 204 réponse est renvoyé sans corps du message.

>[AZURE.NOTE] Vous devez coder URL l’id du travail (normalement nb:jid:UUID : somevalue) lors de son passage dans en tant que paramètre à CancelJob.


### <a name="get-the-output-asset"></a>Obtenir de l’actif de sortie 

Le code suivant montre comment demander l’actif de sortie identifiant. 


**Demande HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Réponse HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>Publier les biens et get diffusion en continu et les URL de téléchargement progressive avec l’API REST

Pour diffuser en continu ou télécharger un bien, vous devez tout d’abord « publier » en créant un repère. Repères permettent d’accéder aux fichiers contenues dans l’actif. Media Services prend en charge les deux types de repères : OnDemandOrigin Locator, utilisé pour diffuser des médias (par exemple, MPEG tiret, TLS ou Smooth Streaming) et Locator Access Signature (sa), utilisé pour télécharger des fichiers multimédias.

Une fois que vous créez les indicateurs, vous pouvez créer les URL qui sont utilisés pour diffuser en continu ou téléchargez vos fichiers. 


Une URL de diffusion en continu de Smooth Streaming format est le suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Une URL de diffusion en continu de TLS a le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Une URL de diffusion en continu de tiret MPEG a le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Une URL de sa permet de télécharger des fichiers a le format suivant :

    {blob container name}/{asset name}/{file name}/{SAS signature}

Cette section montre comment effectuer les tâches suivantes nécessaire de vos biens « publier ».  

- Création de la AccessPolicy avec autorisation de lecture 
- Création d’une URL associations de sécurité pour télécharger le contenu 
- Création d’une URL d’origine pour la diffusion de contenu 

###<a name="creating-the-accesspolicy-with-read-permission"></a>Création de la AccessPolicy avec autorisation de lecture

Avant le téléchargement ou n’importe quel contenu multimédia en continu, tout d’abord définir un AccessPolicy avec des autorisations de lecture et créer l’entité Locator appropriée qui spécifie le type de mécanisme de livraison que vous souhaitez activer pour vos clients. Pour plus d’informations sur les propriétés disponibles, voir [Propriétés de l’entité AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

L’exemple suivant montre comment spécifier la AccessPolicy pour les autorisations de lecture pour une ressource donnée.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

En cas de réussite, un code de 201 réussite est renvoyé décrivant l’entité AccessPolicy que vous avez créé. Vous utilisez l’AccessPolicy Id ainsi que le code d’actif de l’actif qui contient le fichier que vous souhaitez livrer (par exemple, un bien sortie) pour créer l’entité Locator.

>[AZURE.NOTE]
Ce flux de travail est identique à télécharger un fichier lors de la réception d’un bien (comme mentionné précédemment dans cette rubrique). En outre, comme le téléchargement de fichiers, si vous (ou vos clients) devant accéder immédiatement à vos fichiers, définir votre valeur d’heure de début à cinq minutes avant l’heure actuelle. Cette action est nécessaire car il peut y avoir horloge inclinaison entre le client et Media Services. La valeur d’heure de début doit être placé dans le format de date/heure suivant : AAAA-MM-JJThh (par exemple, « 2014-05-23T17:53:50Z »).


###<a name="creating-a-sas-url-for-downloading-content"></a>Création d’une URL associations de sécurité pour télécharger le contenu 

Le code suivant montre comment obtenir une URL qui peut être utilisée pour télécharger un fichier multimédia créé et téléchargés précédemment. La AccessPolicy dispose de jeu d’autorisations de lecture et le chemin d’accès Locator fait référence à une URL de téléchargement associations de sécurité.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

En cas de réussite, la réponse suivante est renvoyée :

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


La propriété de **chemin d’accès** retournée contient l’URL associations de sécurité.

>[AZURE.NOTE]
Si vous téléchargez du contenu de stockage chiffré, vous devez manuellement déchiffrer avant de le rendre, ou utiliser le MediaProcessor déchiffrement stockage dans une tâche de traitement des fichiers transformés en clair à un OutputAsset de sortie et télécharger à partir de cette ressource. Pour plus d’informations sur le traitement, voir Création d’une tâche de codage avec l’API REST de Services de support. En outre, sa URL Locator ne peut pas mettre à jour une fois qu’ils ont été créés. Par exemple, vous ne peut pas réutiliser le même Locator ayant une valeur d’heure de début mis à jour. Il s’agit en raison de la façon dont les associations de sécurité URL sont créées. Si vous voulez accéder à un bien pour téléchargement après qu’un repère a expiré, vous devez créer un nouvel identifiant avec une nouvelle heure de début.

###<a name="download-files"></a>Télécharger des fichiers

Une fois que la AccessPolicy et Locator définir, vous pouvez télécharger des fichiers à l’aide de l’API REST de stockage Azure.  

>[AZURE.NOTE] Vous devez ajouter le nom de fichier pour le fichier que vous voulez télécharger sur la valeur Locator **chemin d’accès** reçue dans la section précédente. Par exemple, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Pour plus d’informations sur l’utilisation des objets BLOB Azure stockage, voir [L’API REST Blob Service](http://msdn.microsoft.com/library/azure/dd135733.aspx).

À la suite de la tâche de codage que vous avez effectué antérieure (codage en jeu MP4 Adaptive), vous avez plusieurs fichiers MP4 que vous pouvez télécharger progressivement. Par exemple :    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>Création d’une URL de diffusion en continu de diffusion en continu du contenu


Le code suivant montre comment créer une adresse URL de diffusion en continu :

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

En cas de réussite, la réponse suivante est renvoyée :

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Pour diffuser une URL d’origine Smooth Streaming dans un lecteur multimédia de diffusion en continu, vous devez ajouter le chemin d’accès propriété avec le nom de la diffusion en continu lisse manifeste fichier, suivi de « / manifeste ».

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Pour diffuser TLS, ajouter (format = m3u8 aapl) après le « / manifeste ».

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Pour diffuser MPEG tiret, ajouter (format = mpd-heure-csf) après le « / manifeste ».

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Lire votre contenu  

Pour vous de flux vidéo, utilisez [Le lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pour tester le téléchargement progressif, collez une URL dans un navigateur (par exemple, Internet Explorer, Chrome, Safari).


##<a name="next-steps-media-services-learning-paths"></a>Étapes suivantes : Media Services rubriques d’apprentissage professionnelles

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>Recherchez d’autres informations ?

Si cette rubrique ne contenait pas ce que vous attendiez, il manque un élément, ou dans une autre façon n’a pas été répond à vos besoins, veuillez nous fournir vos commentaires à l’aide du thread Disqus ci-dessous.



