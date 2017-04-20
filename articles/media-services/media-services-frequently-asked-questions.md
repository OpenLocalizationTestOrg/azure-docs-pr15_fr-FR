<properties 
    pageTitle="Forum aux questions | Microsoft Azure" 
    description="Forum aux questions (FAQ)" 
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


#<a name="frequently-asked-questions"></a>Forum aux questions

##<a name="general-ams-faqs"></a>Questions fréquentes AMS général

Q : comment adapter l’indexation ?

R : les unités réservées sont les mêmes pour les tâches d’encodage et de l’indexation. Suivez les instructions sur [l’échelle de codage réservés unités](media-services-scale-media-processing-overview.md). **Notez** que les performances indexeur n’est pas affecté par Type d’unité réservé.

Q : j’ai téléchargé, codé et publié une vidéo. Quelle est la raison pour laquelle la vidéo n’est pas lu lorsque j’essaie d’y ?

R : une des raisons plus courantes est que vous n’avez pas au moins une unité de diffusion en continu réservée allouée sur le point de terminaison de diffusion en continu à partir de laquelle vous voulez lecture.  Suivez les instructions sur [l’échelle de diffusion en continu réservés unités](media-services-portal-scale-streaming-endpoints.md).

Q : puis-je faire composition sur un flux live ?

R : composition sur flux en direct n’est pas actuellement disponible dans Azure Media Services, et vous devez donc composer préalable sur votre ordinateur.

Q : puis-je utiliser Azure CDN avec la diffusion en continu Live ?

R : Media Services prend en charge l’intégration avec Azure CDN (pour plus d’informations, voir [comment gérer les points de terminaison diffusion en continu dans un compte de Services de support](media-services-portal-manage-streaming-endpoints.md)).  Vous pouvez utiliser Live en continu avec CDN. Azure Media Services fournit des sorties de diffusion en continu, TLS et MPEG-tiret lisse. Tous ces formats utilisent HTTP pour transférer des données et obtenez des avantages de la mise en cache HTTP. Dans la diffusion en continu live réel les données audio/vidéo sont réparties aux fragments et cette fragments individuels mis en cache dans CDN. Uniquement données doit être actualisées sont les données manifeste. CDN actualise régulièrement les données manifeste.

Q : les services signifie Azure Media prennent en charge les images stockage ?

R : Si vous cherchez simplement pour stocker des images JPEG ou PNG, vous devez conserver celles de stockage d’objets Blob Azure. Il est inutile de les placer dans votre compte Media Services, sauf si vous voulez garder associé à votre vidéo ou Audio actifs. Ou, si vous pouvez avoir besoin d’utiliser les images comme les superpositions dans l’encodeur vidéo. Media Encoder Standard prend en charge la superposition d’images en haut des vidéos, et ce n’est qu’elle répertorie JPEG et PNG pris en charge formats de saisie. Pour plus d’informations, voir [Création superpositions](media-services-custom-mes-presets-with-dotnet.md#overlay).

Q : Comment puis-je copier des éléments à partir d’un seul compte Media Services à un autre.

R : pour copier des éléments d’un compte Media Services à un autre à l’aide de .NET, utilisez méthode d’extension [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponible dans le référentiel [Azure Media Services.NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Pour plus d’informations, voir [thread de forum](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) .

Q : quelles sont les caractères pris en charge à l’appellation des fichiers lorsque vous travaillez avec AMS ?

R : Media Services utilise la valeur de la propriété IAssetFile.Name lors de la création d’URL pour le contenu de la diffusion en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Pour cette raison, encodage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères pour cent-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il peut uniquement être une «. » pour l’extension de nom de fichier.


Q : comment se connecter à l’aide de reste ?

R : une fois connecté avec succès à https://media.windows.net, vous recevrez une redirection 301 spécifier un autre Media Services URI. Vous devez effectuer les appels ultérieurs à la nouvelle URI comme décrit dans la [connexion à Media Services à l’aide de l’API REST](media-services-rest-connect-programmatically.md). 


Q : Comment puis-je pivoter une vidéo pendant le processus d’encodage.

R : les [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) prend en charge la rotation par les angles de 90/180/270. Le comportement par défaut est « Automatique », dans lequel il essaie de détecter les métadonnées de rotation dans le fichier MP4/MOV entrant et compense il. Inclure le **Sources de** l’élément suivant à un de le json Présélections défini [ici](http://msdn.microsoft.com/library/azure/mt269960.aspx):
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
