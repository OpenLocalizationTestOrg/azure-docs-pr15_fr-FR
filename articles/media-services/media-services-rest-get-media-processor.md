<properties 
    pageTitle="Comment créer un processeur média | Microsoft Azure" 
    description="Découvrez comment créer un composant de traitement multimédia pour encoder, convertir le format, chiffrer ou déchiffrer du contenu multimédia pour Azure Media Services." 
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


#<a name="how-to-get-a-media-processor-instance"></a>Comment : obtenir une Instance de processeur multimédia


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTE](media-services-rest-get-media-processor.md)

##<a name="overview"></a>Vue d’ensemble

Dans Media Services qu'un processeur média est un composant qui gère une tâche de traitement spécifiques, telles que le codage, mettre en forme de conversion, le chiffrement ou déchiffrement du contenu multimédia. Vous créez généralement un processeur média lorsque vous créez une tâche à coder, chiffrez ou convertir le format du contenu multimédia.

Le tableau suivant fournit le nom et la description de chaque processeur média disponible.

Nom du processeur multimédia|Description|Plus d’informations
---|---|---
Standard de décodage d’éléments multimédias|Fournit les fonctionnalités standards pour le codage à la demande. |[Vue d’ensemble et de comparaison de Azure sur codeurs Media à la demande](media-services-encode-asset.md)
Flux de travail Media Encoder Premium|Vous permet d’exécuter des tâches d’encodage à l’aide de flux de travail Media Encoder Premium.|[Vue d’ensemble et de comparaison de Azure sur codeurs Media à la demande](media-services-encode-asset.md)
Indexer de Media Azure| Permet de rendre disponible pour la recherche du contenu et des fichiers multimédias, ainsi que de générer pistes sous-titrage fermés et des mots clés.|[Indexer de Media Azure](media-services-index-content.md)
Azure Media Hyperlapse (preview)|Vous permet d’atténuer les « bosses » dans votre vidéo avec stabilisation vidéo. Vous permet également d’accélérer votre contenu dans un élément consommable.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Amortissement
Déchiffrement de stockage| Amortissement|
Gestionnaire de médias Azure|Amortissement|
Azure Media chiffreur|Amortissement|

##<a name="get-mediaprocessor"></a>Obtenir MediaProcessor

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services de support, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez entités dans Media Services, vous devez définir en-tête spécifique champs et des valeurs dans vos requêtes HTTP. Pour plus d’informations, voir [le programme d’installation pour le développement d’API REST Media Services](media-services-rest-how-to-use.md).

>Une fois connecté avec succès à https://media.windows.net, vous recevrez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI comme décrit dans la [connexion à Media Services à l’aide de l’API REST](media-services-rest-connect-programmatically.md). 


L’appel reste suivant montre comment obtenir une instance de processeur media par un nom (dans ce cas, **Media Encoder Standard**). 



    
Demande :

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
Réponse :
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
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


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment obtenir une instance de processeur média, accédez à la rubrique [comment encoder un bien](media-services-rest-get-started.md) qui vous montrent comment utiliser la norme de décodage d’éléments multimédias à encoder un bien.
