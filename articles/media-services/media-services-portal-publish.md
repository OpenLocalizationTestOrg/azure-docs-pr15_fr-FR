<properties
    pageTitle="  Publier du contenu avec le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide dans les étapes de la publication de votre contenu à l’aide du portail Azure."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>

# <a name="publish-content-with-the-azure-portal"></a>Publier du contenu avec le portail Azure

> [AZURE.SELECTOR]
- [Portail](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [RESTE](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 

Pour fournir aux utilisateurs une URL qui peut être utilisée pour diffuser en continu ou télécharger votre contenu, vous devez tout d’abord « publier » vos ressources en créant un repère. Repères permettent d’accéder aux fichiers contenues dans l’actif. Media Services prend en charge les deux types de repères : 

- Diffusion en continu Locator (OnDemandOrigin), utilisé pour la diffusion adaptative (par exemple, pour flux MPEG tiret, TLS ou Smooth Streaming). Pour créer un repère de diffusion en continu vos biens doit contenir un fichier .ism. 
- Progressive Locator (sa), utilisé pour la remise de vidéo par téléchargement progressif.


Une URL de diffusion en continu a le format suivant, et vous pouvez l’utiliser pour lire les biens Smooth Streaming.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Pour générer une URL de diffusion en continu de TLS, ajouter (format = m3u8 aapl) à l’URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Pour créer un tiret MPEG URL de diffusion en continu, ajouter (format = mpd-heure-csf) à l’URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Une URL de sa a le format suivant.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Pour plus d’informations, voir [vue d’ensemble du contenu de remise](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Si vous avez utilisé le portail pour créer des repères avant mars 2015, Locator dont la date d’expiration des années à deux ont été créés.  

Pour mettre à jour une date d’expiration sur un repère, utilisez [reste](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou API [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Notez que lorsque vous mettez à jour la date d’expiration d’un repère associations de sécurité, l’URL change.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Pour utiliser le portail de publication d’un bien

Pour utiliser le portail pour publier un bien, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
1. Sélectionnez **paramètres** > **actifs**.
1. Sélectionnez l’élément que vous voulez publier.
1. Cliquez sur le bouton **Publier** .
1. Sélectionnez le type de repère.
2. Appuyez sur **Ajouter**.

    ![Publier](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L’URL est ajoutée à la liste **d’URL publié**.

## <a name="play-content-from-the-portal"></a>Lire le contenu à partir du portail

Le portail Azure fournit un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo souhaitée, puis sur le bouton **lecture** .

![Publier](./media/media-services-portal-vod-get-started/media-services-play.png)

Certaines considérations s’appliquent :

- Vérifiez que la vidéo a été publiée.
- Ce **lecteur multimédia** est lu à partir de la valeur par défaut diffusion en continu de point de terminaison. Si vous souhaitez lire à partir d’un point de terminaison de la diffusion en continu de ceux définis par défaut, cliquez sur pour copier l’URL et utiliser un autre lecteur. Par exemple, [Le lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- Le point de terminaison de diffusion en continu à partir de laquelle vous êtes en continu doit être en cours d’exécution.  
- Pour diffuser à partir d’un point de terminaison de diffusion en continu, vous devez ajouter au moins une unité de diffusion en continu. Pour plus d’informations, consultez [la](media-services-portal-scale-streaming-endpoints.md) rubrique suivante.   

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


