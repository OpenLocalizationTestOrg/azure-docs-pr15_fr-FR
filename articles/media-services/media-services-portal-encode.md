<properties
    pageTitle="Codage un élément à l’aide de Media Encoder Standard avec le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide dans les étapes de codage d’un bien à l’aide de Media Encoder Standard avec le portail Azure."
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


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Codage un élément à l’aide de Media Encoder Standard avec le portail Azure

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 

Lorsque vous travaillez avec Azure Media Services un des scénarios plus courants a décidé de débit adaptive diffusion en continu à vos clients. Media Services prend en charge le débit adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement). Pour préparer vos vidéos débit adaptive diffusion en continu, vous devez encoder votre vidéo source dans des fichiers multi-débit. Vous devez utiliser l’encodeur **Media Encoder Standard** pour coder vos vidéos.  

Media Services fournit également emballage dynamique qui vous permet d’effectuer votre MP4s multi-débit dans les formats de diffusion en continu suivants : MPEG tiret, TLS, Smooth Streaming, durs ou, sans avoir à nouveau empaqueter dans ces formats de diffusion en continu. Emballage dynamique suffit stocker et payer pour les fichiers au format de stockage unique et Media Services génération et répondre à la réponse appropriée basée sur les requêtes à partir d’un client.

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier source dans un jeu de fichiers de MP4 multi-débit (les étapes de codage sont présentés plus loin dans cette section).
- Accéder au moins une unité de diffusion en continu du point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu. Pour plus d’informations, voir [Configuration des points de terminaison de diffusion en continu](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Pour redimensionner le traitement media, consultez [la](media-services-portal-scale-media-processing.md) rubrique suivante.

## <a name="encode-with-the-azure-portal"></a>Encoder grâce au portail Azure

Cette section décrit les étapes à que suivre pour coder votre contenu avec Media Encoder Standard.

1.  Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2.  Dans la fenêtre **paramètres** , sélectionnez **actifs**.  
2.  Dans la fenêtre **actifs** , sélectionnez l’élément que vous souhaitez coder.
3.  Appuyez sur le bouton **Encoder** .
4.  Dans la fenêtre **Encoder un bien** , sélectionnez le processeur « Standard Media Encoder » et un prédéfini. Par exemple, si vous connaissez votre vidéo d’entrée avec une résolution de 1920 x 1080 pixels, vous pouvez utiliser le « H264 débit plusieurs 1080p « prédéfinis. Pour plus d’informations sur les présélections, voir [cet](https://msdn.microsoft.com/library/azure/mt269960.aspx) article –, il est important de sélectionner le paramètre prédéfini qui convient le mieux pour la saisie vidéo. Si vous avez une vidéo basse résolution (640 x 360), puis vous ne devez pas utiliser la valeur par défaut « H264 débit plusieurs 1080p « prédéfinis.
    
    Pour faciliter la gestion, vous avez une possibilité de modifier le nom de la ressource de sortie et le nom de la tâche.
        
    ![Encoder actifs](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Appuyez sur **créer**.


##<a name="next-step"></a>Étape suivante

Vous pouvez surveiller l’avancement de tâche codage grâce au portail Azure, comme décrit dans [cet](media-services-portal-check-job-progress.md) article.  

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


