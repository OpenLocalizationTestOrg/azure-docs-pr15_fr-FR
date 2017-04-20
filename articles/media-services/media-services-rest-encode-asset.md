<properties 
    pageTitle="Comment encoder un bien à l’aide de Media Encoder Standard | Microsoft Azure" 
    description="Découvrez comment utiliser la norme de décodage d’éléments multimédias à encoder du contenu multimédia sur Media Services. Exemples de code utilisent API REST." 
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


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>Comment encoder un bien à l’aide de Media Encoder Standard


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [RESTE](media-services-rest-encode-asset.md)
- [Portail](media-services-portal-encode.md)

##<a name="overview"></a>Vue d’ensemble
Pour diffuser des vidéos numériques sur internet, vous devez compresser le média. Fichiers vidéo numériques sont très volumineux et peuvent être trop volumineux pour faire via internet ou pour les périphériques de vos clients à l’affichage correct. Codage est le processus de compression vidéo et audio pour que vos clients puissent consulter vos supports.

Tâches d’encodage sont une des opérations de traitement plus courantes dans les Services de support. Créer des tâches d’encodage pour convertir des fichiers multimédias à partir d’un codage à un autre. Lorsque vous encodez, vous pouvez utiliser l’encodeur intégré Media Services (Media Encoder Standard). Vous pouvez également utiliser un encodeur fourni par un partenaire Media Services ; encodeurs tiers sont disponibles via la Azure Marketplace. Vous pouvez spécifier les détails d’encodage des tâches à l’aide de chaînes prédéfinies définis pour votre encodeur ou à l’aide de fichiers de configuration prédéfinis. Pour afficher les types de présélections disponibles, voir [Tâches Présélections pour Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Chaque tâche peut avoir une ou plusieurs tâches selon le type de traitement que vous souhaitez exécuter. À l’API REST, vous pouvez créer des tâches et leurs tâches associées dans de deux façons :

- Les tâches peuvent être définis inline via la propriété de navigation de tâches sur des entités de travail, ou
- par le biais du traitement par lots OData.


Il est recommandé de toujours coder vos fichiers mezzanine dans un débit adaptive MP4 définir, puis convertir le jeu le format souhaité à l’aide de l' [Emballage dynamique](media-services-dynamic-packaging-overview.md). Pour tirer parti d’emballage dynamique, vous devez d’abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu. Pour plus d’informations, voir [comment échelle Media Services](media-services-portal-manage-streaming-endpoints.md).

Si votre actif de sortie est stockage chiffré, vous devez configurer stratégie de remise de biens. Pour plus d’informations, voir [stratégie de remise de biens de configuration](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Avant de commencer faisant référence à des processeurs multimédia, vérifiez que vous avez le bon support processeur identifiant. Pour plus d’informations, consultez [Obtenir des processeurs multimédia](media-services-rest-get-media-processor.md).

##<a name="create-a-job-with-a-single-encoding-task"></a>Créer une tâche avec une seule tâche codage

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services de support, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez entités dans Media Services, vous devez définir en-tête spécifique champs et des valeurs dans vos requêtes HTTP. Pour plus d’informations, voir [le programme d’installation pour le développement d’API REST Media Services](media-services-rest-how-to-use.md).

>Une fois connecté avec succès à https://media.windows.net, vous recevrez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI comme décrit dans la [connexion à Media Services à l’aide de l’API REST](media-services-rest-connect-programmatically.md).
>
>Lors de l’utilisation de JSON et en spécifiant pour utiliser le mot-clé **__metadata** dans la demande (par exemple, pour fait référence à un objet lié) vous devez définir l’en-tête **Accepter** au [format JSON détaillée](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): accepter : application/json ; odata = détaillée.

L’exemple suivant vous montre comment créer et publier un projet avec une que tâche configurée pour coder une vidéo à une résolution spécifique et une qualité. Lorsque le codage avec Media Encoder Standard, vous pouvez utiliser préréglages de configuration de tâche spécifiés [ici](http://msdn.microsoft.com/library/mt269960).

Demande :

BILLET https://media.windows.net/API/Jobs Type de contenu HTTP/1.1 : application/json ; odata = accepter détaillée : application/json ; odata = DataServiceVersion détaillée : MaxDataServiceVersion 3.0 : x-ms-version 3.0 : autorisation 2.11 : porteur <token value> 
 x-ms-client-demande-id : 00000000-0000-0000-0000-000000000000 hôte : media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Réponse :
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>Définir le nom de l’élément de sortie

L’exemple suivant montre comment définir l’attribut assetName :

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>Considérations relatives à la

- Propriétés TaskBody doivent utiliser littéral XML pour définir le nombre d’entrées ou de sortie actifs qui seront utilisés par la tâche. La rubrique tâches contient la définition de schéma XML pour le fichier XML.
- Dans la définition TaskBody, chaque interne valeur pour <inputAsset> et <outputAsset> doit être définie en tant que JobInputAsset(value) ou JobOutputAsset(value).
- Une tâche peut avoir plusieurs éléments de sortie. Un seul JobOutputAsset(x) utilisable uniquement une fois comme résultat d’une tâche dans une tâche.
- Vous pouvez spécifier JobInputAsset ou JobOutputAsset comme un bien d’entrée d’une tâche.
- Tâches ne doivent pas former un cycle.
- Le paramètre de valeur que vous passez à JobInputAsset ou JobOutputAsset représente la valeur d’index pour une ressource. Les biens réels sont définis dans les propriétés de navigation InputMediaAssets et OutputMediaAssets sur la définition de l’entité tâche. 
- Étant donné que Media Services est basé sur OData v3, les éléments individuels dans les ensembles de propriétés de navigation InputMediaAssets et OutputMediaAssets sont référencées par une « __metadata : uri « paire nom-valeur.
- InputMediaAssets correspond à un ou plusieurs éléments que vous avez créé dans Media Services. OutputMediaAssets sont créés par le système. Ils ne font pas référencent à un actif existant.
- OutputMediaAssets peuvent être nommés à l’aide de l’attribut assetName. Si cet attribut n’est pas présent, puis le nom de la OutputMediaAsset sera quelle que soit la valeur de texte interne de la <outputAsset> élément est le suffixe la valeur nom de la tâche, ou la valeur de l’Id de la tâche (dans le cas où la propriété nom n’est pas définie). Par exemple, si vous définissez une valeur pour assetName à « Échantillon », la propriété OutputMediaAsset Name être définie à « Échantillon ». Toutefois, si vous n’avez pas défini une valeur pour assetName, mais n’avez défini le nom du travail à « NewJob », le nom OutputMediaAsset serait « _NewJob JobOutputAsset (valeur) ». 


##<a name="create-a-job-with-chained-tasks"></a>Créer une tâche à effectuer les tâches chaînés

Dans de nombreux scénarios d’application, les développeurs souhaitent créer une série de traitement des tâches. Dans les Services de support, vous pouvez créer une série de tâches chaînés. Chaque tâche effectue les opérations de transformation différents et peut utiliser des supports différents processeurs. Les tâches chaînés peuvent transférer un bien à partir d’une tâche à une autre, en effectuant une série linéaire de tâches sur la ressource. Toutefois, les tâches effectuées dans une tâche doivent pas être dans une séquence. Lorsque vous créez une tâche de chaîne, les objets **ITask** chaînés sont créés dans un seul objet **IJob** .

>[AZURE.NOTE] Il existe actuellement une limite de 30 tâches par projet. Si vous avez besoin de bicyclette plus de 30 tâches, créez plusieurs tâches pour contenir les tâches.


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>Considérations relatives à la

Pour activer la chaîne de la tâche :

- Un projet doit avoir au moins 2 tâches
- Doit comporter au moins une tâche dont l’entrée est sortie d’une autre tâche dans la tâche.

## <a name="use-odata-batch-processing"></a>Utiliser le traitement par lots OData 

L’exemple suivant montre comment OData traitement par lots permet de créer un projet et les tâches. Pour plus d’informations sur le traitement par lots, voir la rubrique [Traitement de lot Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>Créer une tâche à l’aide d’un modèle de tâche


Lors du traitement de plusieurs éléments à l’aide d’un ensemble de tâches courantes, JobTemplates sont utiles pour spécifier les paramètres par défaut tâche prédéfinis, ordre des tâches et ainsi de suite.

L’exemple suivant montre comment créer un modèle de tâche avec une inline TaskTemplate défini. Le TaskTemplate utilise la norme de décodage d’éléments multimédias comme le MediaProcessor pour coder le fichier actif ; Cependant, d’autres MediaProcessors peut être utilisée également. 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]Contrairement aux autres entités Media Services, vous devez définir un nouvel identifiant GUID pour chaque TaskTemplate et placer dans la taskTemplateId et la propriété Id dans le corps de votre demande. Le schéma d’identification contenu doit suivre le schéma décrit dans identifier Azure Media Services entités. En outre, JobTemplates ne peut pas être mis à jour. À la place, vous devez créer un nouvel identifiant avec vos modifications mis à jour.
 

En cas de réussite, la réponse suivante est renvoyée :
    
    HTTP/1.1 201 Created
    
    . . .


L’exemple suivant montre comment créer une tâche faisant référence à un Id le modèle de tâche :

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

En cas de réussite, la réponse suivante est renvoyée :
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Étapes suivantes
À présent que vous savez comment créer une tâche pour coder une assset, accédez à la rubrique [Comment à vérifier progression du projet avec Media Services](media-services-rest-check-job-progress.md) .


##<a name="see-also"></a>Voir aussi

[Obtenir des processeurs multimédia](media-services-rest-get-media-processor.md)
