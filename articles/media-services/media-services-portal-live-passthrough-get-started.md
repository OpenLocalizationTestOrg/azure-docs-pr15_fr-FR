<properties 
    pageTitle="Comment effectuer la diffusion en continu live avec codeurs locales à l’aide du portail Azure | Microsoft Azure" 
    description="Ce didacticiel vous guide tout au long des étapes de création d’un canal est configuré pour une livraison directe." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Comment effectuer la diffusion en continu live avec codeurs locales à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [RESTE]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

Ce didacticiel vous guide dans les étapes de l’aide du portail Azure pour créer un **canal** qui est configurée pour une livraison directe. 

##<a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont nécessaires pour terminer le didacticiel :

- Un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 
- Un compte Media Services. Pour créer un compte Media Services, Découvrez [comment créer un compte de Services de support](media-services-portal-create-account.md).
- Une webcam. Par exemple, [Telestream Wirecast encodeur](http://www.telestream.net/wirecast/overview.htm).

Il est vivement recommandé de consulter les articles suivants :

- [Azure Media Services RTMP prennent en charge et Live encodeurs](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Vue d’ensemble de Live vapeur à l’aide d’Azure Media Services](media-services-manage-channels-overview.md)
- [Live en continu avec encodeurs locales qui créent des flux multi-débit](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Scénario de diffusion en continu live courant

Les étapes suivantes décrivent les tâches nécessaires à la création d’applications de diffusion en continu live courantes qui utilisent des canaux sont configurés pour la remise SQL directe. Ce didacticiel montre comment créer et gérer un canal SQL direct et événements en direct.

1. Connectez une caméra vidéo à un ordinateur. Lancer et configurer un encodeur live local qui exporte un flux RTMP ou MP4 fragmenté multi-débit. Pour plus d’informations, voir [prise en charge du RTMP Azure Media Services et encodeurs Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Cette étape peut également être effectuée après avoir créé votre canal.

1. Créer et commencer à un canal SQL direct.
1. Récupérer le canal d’acquisition URL. 

    L’URL d’acquisition est utilisé par l’encodeur live pour envoyer le flux de données pour le canal.
1. Extraire l’URL d’aperçu de canal. 

    Utilisez cette URL pour vérifier que votre canal reçoit correctement le flux en direct.

3. Créer un événement live/programme. 

    Lorsque vous utilisez le portail Azure, création d’un événement en direct crée également un bien. 
      
    >[AZURE.NOTE]Assurez-vous que vous disposez au moins une unité réservée sur le point de terminaison de diffusion en continu à partir duquel vous souhaitez transmettre du contenu de diffusion en continu.
1. Démarrez le programme d’événement/lorsque vous êtes prêt à démarrer la diffusion en continu et d’archivage.
2. Vous pouvez également l’encodeur live peut être signalé pour démarrer une publication. La publication est insérée dans le flux de sortie.
1. Arrêter le programme/événement dès que vous voulez arrêter la diffusion et l’archivage de l’événement.
1. Supprimer l’événement/programme (et vous pouvez également supprimer l’actif).     

>[AZURE.IMPORTANT] Veuillez consulter [la diffusion en continu Live avec codeurs locales qui créent des flux multi-débit](media-services-live-streaming-with-onprem-encoders.md) pour en savoir plus sur les concepts et considérations relatives à la diffusion en continu live avec encodeurs locales et les canaux SQL directes.

##<a name="to-view-notifications-and-errors"></a>Pour afficher les notifications et les erreurs

Si vous souhaitez afficher les notifications et les erreurs obtenus par le portail Azure, cliquez sur l’icône de Notification.

![Notifications](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>Configurer les points de terminaison de diffusion en continu 

Media Services fournit emballage dynamique qui vous permet d’effectuer votre MP4s multi-débit dans les formats de diffusion en continu suivants : MPEG tiret, TLS, Smooth Streaming, durs ou, sans avoir à réorganiser dans ces formats de diffusion en continu. Avec emballage dynamique suffit stocker et payer pour les fichiers au format de stockage unique et Media Services crée et gère la réponse appropriée basée sur les requêtes à partir d’un client.

Pour tirer parti d’emballage dynamique, vous devez obtenir au moins une unité de diffusion en continu pour le point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu.  

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison de la diffusion**. 

2. Cliquez sur la valeur par défaut diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de diffusion en continu, faites glisser le curseur **unités de diffusion en continu** .

    ![Unités de diffusion en continu](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de n’importe quelle unité nouveau peut prendre jusqu'à 20 minutes.
    
##<a name="create-and-start-pass-through-channels-and-events"></a>Créer et commencer aux événements et les canaux SQL directes

Un canal est associé à des événements/programmes qui vous permettent de contrôler la publication et le stockage de segments dans un flux en direct. Chaînes gérer les événements. 
    
Vous pouvez spécifier le nombre d’heures que vous souhaitez conserver le contenu enregistré pour le programme en définissant la longueur de la **Fenêtre d’Archive** . Cette valeur peut être définie à partir d’un minimum de 5 minutes pour un maximum de 25 heures. Longueur de la fenêtre archive détermine également que la quantité maximale de fois que les clients pouvez effectuer une recherche dans le temps à partir de la position actuelle live. Événements peuvent s’exécuter sur le laps de temps spécifié, mais contenu qui se situe derrière la longueur de la fenêtre est supprimé en permanence. Cette valeur de cette propriété détermine également la durée pendant laquelle les manifestes client peuvent croître.

Chaque événement est associé à une ressource. Pour publier l’événement, vous devez créer un repère à la demande pour l’élément associé. Ayant cette locator permet de générer une URL de diffusion en continu que vous pouvez fournir à vos clients.

Un canal prend en charge jusqu'à trois événements simultanément en cours d’exécution afin que vous pouvez créer plusieurs archives du même flux entrant. Cela vous permet de publier et archiver les différentes parties d’un événement selon les besoins. Par exemple, vos besoins consiste à archiver 6 heures d’un programme, mais pour diffuser dernières uniquement les 10 minutes. Pour ce faire, vous devez créer deux programmes s’exécutant simultanément. Un seul programme est défini sur Archiver 6 heures de l’événement, mais le programme n’est pas publié. L’autre programme est défini sur Archiver 10 minutes et ce programme est publié.

Vous ne devez pas réutiliser les événements en direct existants. En revanche, créer et commencer à un événement pour chaque événement.

Démarrer l’événement lorsque vous êtes prêt à démarrer la diffusion en continu et d’archivage. Arrêter le programme chaque fois que vous souhaitez arrêter la diffusion et l’archivage de l’événement. 

Pour supprimer les contenus archivés, arrêter et supprimer l’événement, puis supprimez l’actif associé. Un bien ne peut pas être supprimé si elle est utilisée par un événement ; l’événement doit d’abord être supprimé. 

Même une fois que vous arrêtez et supprimez l’événement, les utilisateurs pourront diffuser votre contenu archivé sous forme de vidéo à la demande, pour tant que vous ne supprimez pas l’actif.

Si vous souhaitez conserver le contenu archivé, mais n’est pas disponible pour la diffusion en continu, supprimez l’URL de diffusion en continu.

###<a name="to-use-the-portal-to-create-a-channel"></a>Pour utiliser le portail pour créer un canal 

Cette section montre comment utiliser l’option **Création rapide** pour créer un canal SQL direct.

Pour plus d’informations sur les canaux SQL directes, voir [Live en continu avec encodeurs locales qui créent des flux multi-débit](media-services-live-streaming-with-onprem-encoders.md).

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , cliquez sur **Live diffusion en continu**. 

    ![Prise en main](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    La fenêtre **en continu** s’affiche.

3. Cliquez sur **Création rapide** pour créer un canal SQL direct avec la RTMP protocole d’acquisition.

    La fenêtre **Créer un nouveau canal** apparaît.
4. Nommez le nouveau canal, puis cliquez sur **créer**. 

    Cette action crée un canal SQL direct avec la RTMP protocole d’acquisition.

##<a name="create-events"></a>Créer des événements

1. Sélectionner un canal auquel vous souhaitez ajouter un événement.
2. Appuyez sur le bouton **Événement Live** .

![Événement](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>URL d’acquisition Get

Une fois que le canal est créé, vous pouvez obtenir acquisition URL que vous souhaitez offrir à l’encodeur live. L’encodeur utilise ces URL saisir un flux en direct.

![Créé](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>L’événement

Pour visionner l’événement, cliquez sur **un espion** dans le portail Azure ou copiez l’URL de diffusion en continu et utilisez un lecteur de votre choix. 
 
![Créé](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Événement en direct obtenir automatiquement convertis en contenu à la demande arrêt.

##<a name="clean-up"></a>Nettoyer

Pour plus d’informations sur les canaux SQL directes, voir [Live en continu avec encodeurs locales qui créent des flux multi-débit](media-services-live-streaming-with-onprem-encoders.md).

- Un canal peut être arrêté uniquement lorsque tous les programmes événements sur le canal ont été arrêtés.  Une fois que le canal est arrêté, il n’entraîne pas les frais. Lorsque vous avez besoin commencer une nouvelle fois, celui-ci possède la même URL d’acquisition afin que vous n’aurez pas besoin de reconfigurer votre encodeur.
- Un canal peut être supprimé uniquement lorsque tous les événements en direct sur le canal ont été supprimés.

##<a name="view-archived-content"></a>Afficher le contenu archivé

Même une fois que vous arrêtez et supprimez l’événement, les utilisateurs pourront diffuser votre contenu archivé sous forme de vidéo à la demande, pour tant que vous ne supprimez pas l’actif. Un bien ne peut pas être supprimé si elle est utilisée par un événement ; l’événement doit d’abord être supprimé. 

Pour gérer vos ressources, sélectionnez **paramètres** , puis cliquez sur **actifs**.

![Actifs](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
