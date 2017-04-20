<properties
    pageTitle=" Échelle de diffusion en continu de points de terminaison avec le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide dans la procédure de mise à l’échelle des points de terminaison de diffusion en continu grâce au portail Azure."
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


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Échelle de diffusion en continu de points de terminaison avec le portail Azure

##<a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 

Lorsque vous travaillez avec Azure Media Services un des scénarios plus courants est offrant des performances vidéo via adaptive débit diffusion en continu à vos clients. Media Services prend en charge le débit adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Media Services fournit emballage dynamique qui vous permet d’effectuer votre débit adaptive contenu MP4 codé en continu les formats pris en charge par Media Services (MPEG tiret, TLS, Smooth Streaming, HDS) juste-à-temps, sans avoir à stocker des versions prédéfinies de chacun de ces formats de diffusion en continu.

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier mezzanine (source) dans un jeu de fichiers de débit adaptive MP4 (les étapes de codage vous trouverez plus loin dans ce didacticiel).  
- Créer au moins une unité de diffusion en continu pour le *point de terminaison de la diffusion* à partir de laquelle vous envisagez de remise votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de diffusion en continu.

Emballage dynamique suffit stocker et payer pour les fichiers au format de stockage unique et Media Services génération et répondre à la réponse appropriée basée sur les requêtes à partir d’un client.

En outre, vous pouvez contrôler la capacité du service de diffusion en continu le point de terminaison pour gérer les besoins croissants de bande passante en ajustant unités de diffusion en continu. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Unités de diffusion en continu vous fournissent les deux capacité de sortie dédié qui peut être acheté par incréments de 200 Mbps et des fonctionnalités supplémentaires les fonctionnalités qui inclut : [emballage dynamique](media-services-dynamic-packaging-overview.md)CDN et intégration configuration avancée. Pour plus d’informations, voir [Gérer les extrémités de diffusion en continu avec le portail Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Échelle de points de terminaison de la diffusion

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , sélectionnez les **points de terminaison de la diffusion**.
3. Cliquez sur le point de terminaison de diffusion en continu que vous souhaitez mettre à l’échelle. 
4. Déplacez le curseur pour spécifier le nombre d’unités de diffusion en continu
 
![Point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Les considérations suivantes s’appliquent :

- L’allocation de n’importe quel nouvelles unités de diffusion en continu peut prendre environ 20 minutes. 
- Pour l’instant, accédant à partir d’une valeur positive de diffusion en continu unités revenir à aucun, peut désactiver la diffusion à la demande pour devenir une heure.
- Le plus grand nombre d’unités spécifiées pour la période de 24 heures est utilisé pour calculer le coût. Pour plus d’informations sur les tarifs plus d’informations, voir [Media Services tarifs Details](http://go.microsoft.com/fwlink/?LinkId=275107).

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


