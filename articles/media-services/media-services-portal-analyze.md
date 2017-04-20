<properties
    pageTitle="Analyser vos supports à l’aide du portail Azure | Microsoft Azure"
    description="Cette rubrique explique comment traiter votre contenu multimédia avec processeurs de médias Media Analytique (PDP) à l’aide du portail Azure."
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


# <a name="analyze-your-media-using-the-azure-portal"></a>Analyser vos supports à l’aide du portail Azure

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="overview"></a>Vue d’ensemble

Azure Media Services Analytique est un ensemble de la reconnaissance vocale et vision composants (à l’échelle de l’entreprise, la conformité, sécurité et envergure) afin de faciliter pour les entreprises et les entreprises à tirer des leçons exploitables à partir de leurs fichiers vidéo. Pour plus d’aperçu de Azure Media Services Analytique, consultez [la](media-services-analytics-overview.md) rubrique suivante. 

Cette rubrique explique comment traiter votre contenu multimédia avec processeurs de médias Media Analytique (PDP) à l’aide du portail Azure. Média Analytique PDP produire des fichiers MP4 ou JSON. Si un processeur média produit un fichier MP4, vous pouvez progressivement télécharger le fichier. Si un processeur média a donné un fichier JSON, vous pouvez télécharger le fichier à partir du stockage blob Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Choisissez un bien que vous souhaitez analyser 
 
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , sélectionnez **actifs**.  
.
    ![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. Sélectionnez l’élément que vous voulez analyser, puis appuyez sur le bouton **analyser** .
        
    ![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. Dans la fenêtre de **biens multimédias de processus avec Media Analytique** , sélectionnez le processeur. 

    Le reste de cet article explique pourquoi et comment utiliser chaque processeur. 
   
4. Appuyez sur **créer** pour le début une tâche.

## <a name="azure-media-indexer"></a>Indexer de Media Azure

Processeur média **Azure Media indexeur** vous permet Vérifiez que vous pourrez rechercher du contenu et des fichiers multimédias, ainsi que de générer pistes sous-titrage fermés. Cette section fournit des détails sur les options que vous pouvez spécifier pour ce point de gestion.

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Langue

Langage naturel à reconnue dans le fichier multimédia. Par exemple, anglais ou en espagnol. 

### <a name="captions"></a>Légendes

Vous pouvez choisir un format de la légende qui sera généré à partir de votre contenu. Une opération d’indexation peut générer des sous-titres fichiers aux formats suivants :  

- **Lapon**
- **TTML**
- **WebVTT**

Fermé légende (CC) fichiers aux formats suivants peuvent être utilisés pour rendre les fichiers audio et vidéo accessible aux personnes présentant un handicap Audition.

### <a name="aib-file"></a>Fichier AIB

Sélectionnez cette option si vous souhaitez générer le fichier Audio Index Blob pour une utilisation avec ce filtre personnalisé SQL Server comporte. Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) billet de blog.

### <a name="keywords"></a>Mots clés

Sélectionnez cette option si vous souhaitez générer un fichier XML de mots clés. Ce fichier contient des mots clés extraits du contenu vocale, avec la fréquence et les informations d’offset.

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller l’avancement d’une tâche. 

### <a name="output-file"></a>Fichier de copie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse

Azure Media Hyperlapse est un Pack d’administration qui crée lisses vidéos temps écoulé à partir de la première personne ou l’appareil photo de l’action du contenu.  Pour plus d’informations, consultez [la](media-services-hyperlapse-content.md) rubrique suivante. Cette section fournit des détails sur les options que vous pouvez spécifier pour ce point de gestion.

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Vitesse 

Spécifier la vitesse avec lesquelles vous pouvez accélérer la vidéo d’entrée. Le résultat est un rendu de stabilisées et temps écoulé de l’entrée vidéo.

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller l’avancement d’une tâche. 

### <a name="output-file"></a>Fichier de copie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 

## <a name="azure-media-face-detector"></a>Détection de visage Azure Media

La **Détection de visage Azure Media** media processeur () vous permet de compter, suivre les mouvements et même évaluer la participation du public et réaction via des expressions du visage. Ce service propose deux fonctionnalités : 

- **Détection de visage**

    La détection de visage recherche et effectue le suivi des faces humaines dans une vidéo. Plusieurs faces peuvent être détectées et par la suite être suivis qu’ils sont en déplacement, avec les métadonnées de temps et l’emplacement renvoyées dans un fichier JSON. Lors du suivi, il tentera d’attribuer un ID cohérent à la même face alors que la personne est déplacement sur l’écran, même s’ils sont coupés ou laissez brièvement le cadre.

    >[AZURE.NOTE]Ce service n’effectue pas de reconnaissance du visage. Une personne qui laisse le cadre ou devient coupée pour trop longtemps sera attribué un identifiant lorsqu’ils renvoient.

- **Détection des émotions**
    
    Détection des émotions est un composant facultatif du processeur Media détection nominale qui renvoie analyse sur plusieurs attributs émotionnels de faces détectés, notamment bonheur, leur, peur, anger et plus encore. 

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Mode de détection

Un des modes suivants peut être utilisé par le processeur :

- détection de visage
- par la détection de visage émotions
- détection des émotions agrégation

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller l’avancement d’une tâche. 

### <a name="output-file"></a>Fichier de copie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 

## <a name="azure-media-motion-detector"></a>Détection de mouvement Azure Media

La **Détection de mouvement Azure Media** media processeur () permet d’identifier efficacement les sections utiles au sein d’une vidéo dans le cas contraire versions longue et se déroule normalement. Détection de mouvement peut être utilisée dans une partie caméra statique pour identifier les sections de la vidéo où mouvement s’est produite. Il génère un fichier JSON contenant une entrée de métadonnées avec horodatages et la région de cadre englobant dans lequel l’événement s’est produite.

Ciblée sur flux vidéo de sécurité, cette technologie est en mesure de classer mouvement en fausses tels que les ombres et les changements d’éclairage et événements appropriés. Permet de générer des alertes de sécurité à partir de flux vidéo sans recevoir des messages indésirables avec des événements non pertinents illimitées, tout en la possibilité extraire instants d’intérêt de vidéos de surveillance longs.

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Miniatures de vidéo Azure Media

Ce processeur peut vous aider à créer des synthèses des vidéos longues en sélectionnant automatiquement extraits intéressantes à partir de la vidéo source. Cela est utile lorsque vous souhaitez fournir un bref aperçu à quoi s’attendre dans une vidéo longue. Pour plus d’informations et d’exemples, voir [Utiliser Azure Media Video les miniatures pour créer une synthèse vidéo](media-services-video-summarization.md)

![Analyser des vidéos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller l’avancement d’une tâche. 

### <a name="output-file"></a>Fichier de copie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 


##<a name="next-steps"></a>Étapes suivantes

Afficher les Services de support rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


