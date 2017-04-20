<properties 
    pageTitle="Créer des flux de travail de codage avancées avec le Concepteur de flux de travail | Microsoft Azure" 
    description="Découvrez comment créer des flux de travail de codage avancées avec le Concepteur de flux de travail." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Créer des flux de travail de codage avancées avec le Concepteur de flux de travail

##<a name="overview"></a>Vue d’ensemble

Le **Concepteur de flux de travail** est un outil de bureau de Windows qui permet de concevoir et créer des flux de travail personnalisés pour le codage de **Flux de travail Media Encoder Premium**.
À l’aide de la puissance de l’outil du Concepteur de flux de travail, vous pouvez concevoir et créer des flux de travail complexe qui est exécuté en **Media Encoder Premium**.  

Flux de travail peut inclure logique de décision de client et branchement en fonction de propriétés du fichier source d’entrée. Vous pouvez créer des flux de travail avec propriétés non prioritaires et les valeurs dynamiques pour faciliter la même les tâches d’encodage plus complexes répéter et personnaliser dans le cloud.

Exemple de flux de travail que vous pouvez créer inclure :

- Décision en fonction de flux de travail qui inspecte le contenu source pour la résolution et code uniquement les pistes de sortie souhaité.  Il s’agit helfpul en supprimant les pistes perdus qui serait générés par dimensionnement vers la par inadvertance contenu source.
- Plusieurs fichiers d’entrée peuvent être utilisés pour prendre en charge des légendes, superpositions et SUTURE contenu solidaires. 

Cet outil peut également être utilisé pour modifier un de nos [publié le flux de travail](media-services-workflow-designer.md#existing_workflows). 

>[AZURE.NOTE]Pour obtenir une copie de l’outil Concepteur de flux de travail, veuillez contacter mepd@microsoft.com.


Lorsqu’un fichier de flux de travail est créé, il peut être téléchargé en tant qu’actif et ensuite être utilisé pour le codage de fichiers multimédias. Pour plus d’informations sur la façon de coder avec **Flux de travail Media Encoder Premium** à l’aide de **.NET**, voir [avancées de codage de flux de travail Media Encoder Premium](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modifier le flux de travail existant

La valeur par défaut [publié le flux de travail](media-services-workflow-designer.md#existing_workflows) peut être modifié à l’aide de l’outil de conception. Vous pouvez accéder à la valeur par défaut des fichiers de flux de travail [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient également la description de ces fichiers.

Les vidéos suivantes montrent comment utiliser le concepteur.

###<a name="day-1--getting-started"></a>Jour 1 – mise en route

Jour 1 vidéo explique comment :

- Vue d’ensemble du Concepteur
- Flux de travail simple – « Bonjour tout le monde »
- Créer plusieurs fichiers MP4 pour une utilisation avec Azure Media Services diffusion en continu de sortie

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>Jour 2

Jour 2 vidéo explique comment :

- Variations de scénarios du fichier Source : gestion des données audio
- Flux de travail avec une logique avancée
- Étapes de graphique

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>3 jours

Jour 3 vidéo explique comment :

- L’écriture de script à l’intérieur de flux de travail/projets
- Restrictions avec l’encodeur en cours
- Q & A
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Si vous devez prendre en charge ou avez des questions sur la création de flux de travail personnalisés dans l’outil du Concepteur de flux de travail, envoyez un courrier électronique à mepd@microsoft.com.

##<a name="see-also"></a>Voir aussi

[Vidéos de formation du Concepteur de flux de travail de décodage d’éléments Premium Azure](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)
