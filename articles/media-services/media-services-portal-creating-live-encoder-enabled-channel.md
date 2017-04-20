<properties 
    pageTitle="Comment effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-débit grâce au portail Azure | Microsoft Azure" 
    description="Ce didacticiel vous guide tout au long des étapes de création d’un canal qui reçoit un flux en direct unique débit et code au flux multi-débit à l’aide du portail Azure." 
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
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Comment effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-débit grâce au portail Azure

> [AZURE.SELECTOR]
- [Portail](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Ce didacticiel vous guide tout au long des étapes de création d’un **canal** qui reçoit un flux en direct unique débit et code au flux multi-débit.

>[AZURE.NOTE]Pour plus d’informations conceptuelles associés aux canaux activés pour le codage live, voir [Live en continu à l’aide d’Azure Media Services pour créer des flux multi-débit](media-services-manage-live-encoder-enabled-channels.md).

##<a name="common-live-streaming-scenario"></a>Scénario de diffusion en continu Live courant

Voici les étapes générales permettant de créer des applications de diffusion en continu live courantes.

>[AZURE.NOTE] Pour l’instant, la durée maximale recommandée d’un événement en direct est 8 heures. Veuillez contacter amslived Microsoft.com si vous avez besoin exécuter un canal pour longtemps.

1. Connectez une caméra vidéo à un ordinateur. Lancer et configurer un encodeur live local qui peut afficher un flux de débit unique dans un des protocoles suivants : RTMP, Smooth Streaming ou RTP (MPEG-TS). Pour plus d’informations, voir [prise en charge du RTMP Azure Media Services et encodeurs Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Cette étape peut également être effectuée après avoir créé votre canal.

1. Créer et commencer à un canal. 

1. Récupérer le canal d’acquisition URL. 

    L’URL d’acquisition est utilisé par l’encodeur live pour envoyer le flux de données pour le canal.
1. Extraire l’URL d’aperçu de canal. 

    Utilisez cette URL pour vérifier que votre canal reçoit correctement le flux en direct.

3. Créer un événement/programme (qui permet de créer également un bien). 
1. Publier l’événement (qui permet de créer un repère à la demande pour l’actif associé).  

    Assurez-vous que vous disposez au moins une unité réservée sur le point de terminaison de diffusion en continu à partir duquel vous souhaitez transmettre du contenu de diffusion en continu.
1. Démarrer l’événement lorsque vous êtes prêt à démarrer la diffusion en continu et d’archivage.
2. Vous pouvez également l’encodeur live peut être signalé pour démarrer une publication. La publication est insérée dans le flux de sortie.
1. Arrêter l’événement dès que vous voulez arrêter la diffusion et l’archivage de l’événement.
1. Supprimer l’événement (et vous pouvez également supprimer l’actif).   

##<a name="in-this-tutorial"></a>Dans ce didacticiel

Dans ce didacticiel, le portail Azure est utilisé pour accomplir les tâches suivantes : 

2.  Configurez des points de diffusion en continu.
3.  Créer un canal est activé pour effectuer le codage live.
1.  Obtenir l’URL d’acquisition afin de lui fournir actives décodage d’éléments. L’encodeur live va utiliser cette URL pour le flux d’acquisition dans le canal. .
1.  Créer un événement/programme (et une ressource)
1.  Publier l’actif et obtenir l’URL de diffusion en continu  
1.  Lire votre contenu 
2.  Nettoyage des

##<a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont requis pour terminer le didacticiel.

- Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- Un compte Media Services. Pour créer un compte Media Services, voir [Créer un compte](media-services-portal-create-account.md).
- Une webcam et un encodeur qui peut envoyer un flux en direct débit unique.

##<a name="configure-streaming-endpoints"></a>Configurer les points de terminaison de diffusion en continu 

Media Services fournit emballage dynamique qui vous permet d’effectuer votre MP4s multi-débit dans les formats de diffusion en continu suivants : MPEG tiret, TLS, Smooth Streaming, durs ou, sans avoir à nouveau empaqueter dans ces formats de diffusion en continu. Emballage dynamique suffit stocker et payer pour les fichiers au format de stockage unique et Media Services génération et répondre à la réponse appropriée basée sur les requêtes à partir d’un client.

Pour tirer parti d’emballage dynamique, vous devez obtenir au moins une unité de diffusion en continu pour le point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu.  

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Connectez-vous sur le [portail Azure](https://portal.azure.com/) et sélectionnez votre compte AMS.
1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison de la diffusion**. 

2. Cliquez sur la valeur par défaut diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de diffusion en continu, faites glisser le curseur **unités de diffusion en continu** .

    ![Unités de diffusion en continu](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de n’importe quelle unité nouveau peut prendre jusqu'à 20 minutes.

##<a name="create-a-channel"></a>Créer un canal

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez Media Services, puis cliquez sur le nom de votre compte Media Services.
2. Sélectionnez **Live diffusion en continu**.
3. Sélectionnez **créer personnalisé**. Cette option vous permet de créer un canal qui est activé pour le codage live.

    ![Créer un canal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. Cliquez sur **paramètres**.
    
    1.  Choisissez le type de canal **Encodage Live** . Ce type indique que vous voulez créer un canal qui est activé pour le codage de live. Cela signifie que l’entrant débit unique flux est envoyé au canal et codée dans un flux multi-débit à l’aide de paramètres encoder live spécifié. Pour plus d’informations, voir [Live en continu à l’aide d’Azure Media Services pour créer des flux multi-débit](media-services-manage-live-encoder-enabled-channels.md). Cliquez sur OK.
    2. Spécifiez le nom d’un canal.
    3. Cliquez sur OK dans la partie inférieure de l’écran.
    
5. Sélectionnez l’onglet **acquisition** .

    1. Dans cette page, vous pouvez sélectionner un protocole de diffusion en continu. Pour le type de canal **Encodage Live** , options de protocole valides sont :
        
        - Seul débit Fragmented MP4 (Smooth Streaming)
        - Débit unique RTMP
        - RTP (MPEG-TS) : MPEG-2 Transport Stream sur RTP.
        
        Pour une explication détaillée sur chaque protocole, voir [Live en continu à l’aide d’Azure Media Services pour créer des flux multi-débit](media-services-manage-live-encoder-enabled-channels.md).
    
        Vous ne pouvez pas modifier l’option de protocole pendant le canal ou ses événements/programmes associés en cours d’exécution. Si vous avez besoin des protocoles différents, vous devez créer des canaux distincts pour chaque protocole de diffusion en continu.  

    2. Vous pouvez appliquer restriction IP de l’acquisition. 
    
        Vous pouvez définir les adresses IP autorisées pour ajouter une vidéo à ce canal. Autorisé adresses IP peuvent être spécifiés comme une seule adresse IP (par exemple, « 10.0.0.1 »), une plage d’IP à l’aide d’une adresse IP et un masque CIDR (par exemple, « 10.0.0.1/22 ») ou une plage IP à l’aide d’une adresse IP et un masque en numérotation décimale (par exemple, « 10.0.0.1(255.255.252.0)').

        Si aucune adresses IP ne sont spécifiées et il n’existe aucune définition de la règle puis aucune adresse IP n’est autorisé. Pour permettre à n’importe quelle adresse IP, créez une règle et définissez 0.0.0.0/0.

6. Sous l’onglet **Aperçu** , appliquez restriction IP dans l’aperçu.
7. Sous l’onglet **codage** , spécifiez le codage prédéfini. 

    Pour l’instant, le seul système prédéfinis vous pouvez sélectionner est **par défaut 720p**. Pour spécifier un paramètre prédéfini, ouvrez un tickets de support technique Microsoft. Ensuite, entrez le nom de la prédéfini créé pour vous. 

>[AZURE.NOTE] Pour l’instant, le début de canal peut prendre jusqu'à 30 minutes. Réinitialisation du canal peut prendre jusqu'à 5 minutes.

Une fois que vous avez créé le canal, vous pouvez cliquez sur le canal et sélectionnez **paramètres** dans laquelle vous pouvez afficher vos configurations de canaux. 

Pour plus d’informations, voir [Live en continu à l’aide d’Azure Media Services pour créer des flux multi-débit](media-services-manage-live-encoder-enabled-channels.md).


##<a name="get-ingest-urls"></a>URL d’acquisition Get

Une fois que le canal est créé, vous pouvez obtenir acquisition URL que vous souhaitez offrir à l’encodeur live. L’encodeur utilise ces URL saisir un flux en direct.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>Créer et gérer des événements

###<a name="overview"></a>Vue d’ensemble

Un canal est associé à des événements/programmes qui vous permettent de contrôler la publication et le stockage de segments dans un flux en direct. Chaînes gérer les programmes d’événements. La relation de canal et du même programme est très similaire au support traditionnel où un canal possède un flux constant de contenu et un programme portée est limitée à certains événement chronométré sur ce canal.

Vous pouvez spécifier le nombre d’heures que vous souhaitez conserver le contenu enregistré pour l’événement en définissant la longueur de la **Fenêtre d’Archive** . Cette valeur peut être définie à partir d’un minimum de 5 minutes pour un maximum de 25 heures. Longueur de la fenêtre archive détermine également que la quantité maximale de fois que les clients pouvez effectuer une recherche dans le temps à partir de la position actuelle live. Événements peuvent s’exécuter sur le laps de temps spécifié, mais contenu qui se situe derrière la longueur de la fenêtre est supprimé en permanence. Cette valeur de cette propriété détermine également la durée pendant laquelle les manifestes client peuvent croître.

Chaque événement est associé à une ressource. Pour publier l’événement, vous devez créer un repère à la demande pour l’élément associé. Ayant cette locator activer vous permet de générer une URL de diffusion en continu que vous pouvez fournir à vos clients.

Un canal prend en charge jusqu'à trois événements simultanément en cours d’exécution afin que vous pouvez créer plusieurs archives du même flux entrant. Cela vous permet de publier et archiver les différentes parties d’un événement selon les besoins. Par exemple, vos besoins consiste à archiver 6 heures d’un événement, mais pour diffuser dernières uniquement les 10 minutes. Pour ce faire, vous devez créer deux qui s’exécutent simultanément événement. Un événement est défini sur Archiver 6 heures de l’événement, mais le programme n’est pas publié. L’autre événement est définie sur Archiver 10 minutes et ce programme est publié.

Vous ne devez pas réutiliser programmes existants pour les nouveaux événements. En revanche, créer et démarrer un nouveau programme pour chaque événement.

Démarrer un événement/programme lorsque vous êtes prêt à démarrer la diffusion en continu et d’archivage. Arrêter l’événement dès que vous voulez arrêter la diffusion et l’archivage de l’événement. 

Pour supprimer les contenus archivés, arrêter et supprimer l’événement, puis supprimez l’actif associé. Un bien ne peut pas être supprimé si elle est utilisée par l’événement ; l’événement doit d’abord être supprimé. 

Même une fois que vous arrêtez et supprimez l’événement, les utilisateurs pourront diffuser votre contenu archivé sous forme de vidéo à la demande, pour tant que vous ne supprimez pas l’actif.

Si vous souhaitez conserver le contenu archivé, mais n’est pas disponible pour la diffusion en continu, supprimez l’URL de diffusion en continu.

###<a name="createstartstop-events"></a>Événements créer/début/fin

Une fois que le flux de données s’étalant dans le canal, vous pouvez commencer l’événement de diffusion en continu en créant un bien, de programme et Locator diffusion en continu. Cela archiver le flux et rendre disponible aux visiteurs via le point de terminaison de la diffusion. 

Il existe deux façons de démarrer événement : 

1. Dans la page **d’un canal** , appuyez sur **Événement Live** à ajouter un nouvel événement.

    Spécifiez : nom de l’événement, nom de la ressource, fenêtre Archiver et chiffrement option.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Si vous vous étiez **publier cet événement en direct désormais** activé, l’événement les URL publication sera créée.
    
    Vous pouvez appuyer sur **Démarrer**, chaque fois que vous êtes prêt à l’événement de flux.

    Une fois que vous démarrez l’événement, vous pouvez appuyer sur **Espion** pour commencer à lire le contenu.

2. Par ailleurs, vous pouvez utiliser un raccourci et appuyez sur la page de **canal** bouton **Mise en service** . Cela crée une valeur par défaut bien, de programme et de diffusion en continu Locator.

    L’événement est appelé **par défaut** et la fenêtre d’archive est définie sur 8 heures.

Vous pouvez visionner l’événement publié à partir de la page **Live événement** . 

Si vous cliquez sur **Désactiver l’Air**, il arrêtera tous les événements en direct. 


##<a name="watch-the-event"></a>L’événement

Pour visionner l’événement, cliquez sur **un espion** dans le portail Azure ou copiez l’URL de diffusion en continu et utilisez un lecteur de votre choix. 
 
![Créé](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Événement en direct convertit automatiquement les événements au contenu à la demande arrêt.

##<a name="clean-up"></a>Nettoyer

Si vous avez fini de diffusion en continu les événements et que vous souhaitez nettoyer les ressources sa mise en service précédemment, suivez la procédure suivante.

- Arrêtez de pousser le flux de données à partir de l’encodeur.
- Arrêter le canal. Une fois que le canal est arrêté, il n’entraînera pas les frais. Lorsque vous avez besoin commencer une nouvelle fois, celui-ci possède la même URL d’acquisition afin que vous n’aurez pas besoin de reconfigurer votre encodeur.
- Vous pouvez arrêter votre point de terminaison diffusion en continu, sauf si vous voulez continuer à fournir l’archivage de votre événement en direct sous forme de flux à la demande. Si le canal est dans un état arrêté, il n’entraînera pas les frais.
  
##<a name="view-archived-content"></a>Afficher le contenu archivé

Même une fois que vous arrêtez et supprimez l’événement, les utilisateurs pourront diffuser votre contenu archivé sous forme de vidéo à la demande, pour tant que vous ne supprimez pas l’actif. Un bien ne peut pas être supprimé si elle est utilisée par un événement ; l’événement doit d’abord être supprimé. 

Pour gérer vos ressources, sélectionnez **paramètres** , puis cliquez sur **actifs**.

![Actifs](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>Considérations relatives à la

- Pour l’instant, la durée maximale recommandée d’un événement en direct est 8 heures. Veuillez contacter amslived Microsoft.com si vous avez besoin exécuter un canal pour longtemps.
- Assurez-vous que vous disposez au moins une unité réservée sur le point de terminaison de diffusion en continu à partir duquel vous souhaitez transmettre du contenu de diffusion en continu.


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
