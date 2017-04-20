<properties
    pageTitle="Vue d’ensemble de l’emballage dynamique | Microsoft Azure"
    description="La rubrique donne et en vue d’ensemble de l’emballage dynamique."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>Emballage dynamique

##<a name="overview"></a>Vue d’ensemble

Formats de protection du contenu à une variété de technologies d’assistance client et Microsoft Azure Media Services peuvent servir à transmettre nombreux media source fichier formats, médias en continu formats, (par exemple, iOS, XBOX, Silverlight, Windows 8). Ces clients comprennent différents protocoles, par exemple iOS nécessite un format de diffusion en continu Live HTTP (TLS) V4 et Silverlight et Xbox nécessitent Smooth Streaming. Si vous disposez d’un ensemble de débit adaptatif (multi-débit) MP4 fichiers (ISO Base média 14496-12) ou un jeu de fichiers Smooth Streaming adaptive débit que vous souhaitez répondre aux clients qui comprennent MPEG tiret, TLS ou Smooth Streaming, vous devez tirer parti d’emballage dynamique Media Services.

Avec emballage dynamique, il vous suffit consiste à créer un bien qui contient un jeu de fichiers de débit adaptive MP4 ou Smooth Streaming débit adaptive. Puis, selon le format spécifié dans la demande manifeste ou fragment, la diffusion en continu à la demande serveur assuré de recevoir le flux de données dans le protocole que vous avez choisi. Par conséquent, vous ne devez stocker et payer pour les fichiers au format de stockage unique et services de support pour la génération et répondre à la réponse appropriée basée sur les requêtes à partir d’un client.

Le diagramme suivant montre le flux de travail emballage statique et d’encodage traditionnel.

![Codage statique](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Le diagramme suivant illustre le flux de travail emballage dynamique.

![Codage dynamique](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Pour tirer parti d’emballage dynamique, vous devez d’abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu. Pour plus d’informations, voir [comment échelle Media Services](media-services-portal-manage-streaming-endpoints.md).

##<a name="common-scenario"></a>Scénarios courants

1. Télécharger un fichier d’entrée (appelé un fichier mezzanine). Par exemple, H.264, MP4 ou WMV (pour la liste des formats pris en charge, consultez [Formats pris en charge par Media Encoder Standard](media-services-media-encoder-standard-formats.md).

1. Coder votre fichier mezzanine aux ensembles de débit adaptive H.264 MP4.

1. Publier les biens qui contient le débit adaptive MP4 configurer en créant le point de suite à la demande.

1. Créer les URL de diffusion en continu pour accéder à et diffuser votre contenu.


##<a name="preparing-assets-for-dynamic-streaming"></a>Préparation des ressources pour le flux dynamique

Pour préparer votre actif pour le flux dynamique que vous avez deux possibilités :

1. [Télécharger un fichier maître](media-services-dotnet-upload-files.md).
2. [Utiliser l’encodeur Media Encoder Standard pour produire des ensembles de débit adaptive H.264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Flux de votre contenu](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formats qui ne sont pas pris en charge par emballage dynamique

Formats de fichier source suivants ne sont pas pris en charge par emballage dynamique.

- Fichiers Dolby digital mp4.
- Dolby digital lisse les fichiers.

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
