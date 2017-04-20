<properties
    pageTitle=" Prise en main diffuser du contenu à la demande à l’aide du portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide dans les étapes de l’implémentation d’un service de distribution de contenu vidéo à la demande (VoD) simple avec application Azure Media Services (AMS) à l’aide du portail Azure."
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Prise en main diffuser du contenu à la demande à l’aide du portail Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Ce didacticiel vous guide dans les étapes de l’implémentation d’un service de distribution de contenu vidéo à la demande (VoD) simple avec application Azure Media Services (AMS) à l’aide du portail Azure.

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 

Ce didacticiel inclut les tâches suivantes :

1.  Créez un compte Azure Media Services.
2.  Configurer le point de terminaison de diffusion en continu.
1.  Télécharger un fichier vidéo.
1.  Encoder le fichier source dans un jeu de fichiers MP4 de débit adaptive.
1.  Publier les biens et les get diffusion en continu et les URL de téléchargement progressive.  
1.  Lire votre contenu.


## <a name="create-an-azure-media-services-account"></a>Créer un compte Azure Media Services

Les étapes décrites dans cette section montrent comment créer un compte AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Web + Mobile** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** Entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte Media Services est tous les nombres en minuscules ou des lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure que vous avez accès.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. En savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans un **emplacement**, sélectionnez la région géographique est utilisé pour stocker les enregistrements de métadonnées et éléments multimédias de votre compte Media Services. Cette zone est utilisée pour traiter et diffuser vos médias. Uniquement les zones des Services de support disponibles apparaissent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique comme votre compte Media Services, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles de noms de compte de stockage sont les mêmes que pour les comptes Media Services.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour afficher la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, Encoder actifs, surveiller l’avancement de tâche) utilisez la fenêtre **paramètres** .

## <a name="manage-keys"></a>Gérer les clés

Vous devez le nom du compte et les informations de clé primaires par programme pour accéder à la Media Services compte.

1. Dans le portail Azure, sélectionnez votre compte. 

    La fenêtre **paramètres** apparaît à droite. 

2. Dans la fenêtre **paramètres** , sélectionnez **clés**. 

    Les fenêtres de **gérer des clés** affiche le nom du compte et les clés principales et secondaires s’affiche. 
3. Appuyez sur le bouton Copier pour copier les valeurs.
    
    ![Media Services clés](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Configurer les points de terminaison de diffusion en continu

Lorsque vous travaillez avec Azure Media Services un des scénarios plus courants est offrant des performances vidéo via adaptive débit diffusion en continu à vos clients. Media Services prend en charge le débit adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Media Services fournit emballage dynamique qui vous permet d’effectuer votre débit adaptive contenu MP4 codé en continu les formats pris en charge par Media Services (MPEG tiret, TLS, Smooth Streaming, HDS) juste-à-temps, sans avoir à stocker des versions prédéfinies de chacun de ces formats de diffusion en continu.

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier mezzanine (source) dans un jeu de fichiers de débit adaptive MP4 (les étapes de codage vous trouverez plus loin dans ce didacticiel).  
- Créer au moins une unité de diffusion en continu pour le *point de terminaison de la diffusion* à partir de laquelle vous envisagez de remise votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de diffusion en continu.

Avec emballage dynamique, vous ne devez stocker et payer pour les fichiers au format de stockage unique et Media Services crée et gère la réponse appropriée basée sur les requêtes à partir d’un client.

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :


1. Dans la fenêtre **paramètres** , cliquez sur **les points de terminaison de la diffusion**. 

2. Cliquez sur la valeur par défaut diffusion en continu de point de terminaison. 

    La fenêtre **Détails de point de terminaison de diffusion en continu par défaut** s’affiche.

3. Pour spécifier le nombre d’unités de diffusion en continu, faites glisser le curseur **unités de diffusion en continu** .

    ![Unités de diffusion en continu](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

    >[AZURE.NOTE]L’allocation de n’importe quelle unité nouveau peut prendre jusqu'à 20 minutes.

## <a name="upload-files"></a>Télécharger des fichiers

Au flux vidéos à l’aide d’Azure Media Services, vous devez télécharger les vidéos source et les coder dans plusieurs vitesses de transmission publier le résultat. La première étape est décrite dans cette section. 

1. Dans la fenêtre **paramètre** , cliquez sur **ressources**.

    ![Télécharger des fichiers](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Cliquez sur le bouton **Télécharger** .

    La fenêtre **télécharger un élément vidéo** apparaît.

    >[AZURE.NOTE] Il n’existe aucune limite de taille de fichier.
    
4. Accédez à la vidéo de votre choix sur votre ordinateur, sélectionnez-le, puis appuyez sur OK.  

    Le téléchargement démarre et vous pouvez voir la progression sous le nom du fichier.  

Une fois le téléchargement terminé, vous voyez le nouvel actif figurant dans la fenêtre **actifs** . 

## <a name="encode-assets"></a>Encoder actifs

Lorsque vous travaillez avec Azure Media Services un des scénarios plus courants a décidé de débit adaptive diffusion en continu à vos clients. Media Services prend en charge le débit adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement). Pour préparer vos vidéos débit adaptive diffusion en continu, vous devez encoder votre vidéo source dans des fichiers multi-débit. Vous devez utiliser l’encodeur **Media Encoder Standard** pour coder vos vidéos.  

Media Services fournit également emballage dynamique qui vous permet d’effectuer votre MP4s multi-débit dans les formats de diffusion en continu suivants : MPEG tiret, TLS, Smooth Streaming, durs ou, sans avoir à réorganiser dans ces formats de diffusion en continu. Avec emballage dynamique, vous ne devez stocker et payer pour les fichiers au format de stockage unique et Media Services crée et gère la réponse appropriée basée sur les requêtes à partir d’un client.

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier source dans un jeu de fichiers de MP4 multi-débit (les étapes de codage sont présentés plus loin dans cette section).
- Accéder au moins une unité de diffusion en continu du point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu. Pour plus d’informations, voir [Configuration des points de terminaison de diffusion en continu](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Pour utiliser le portail pour encoder

Cette section décrit les étapes à que suivre pour coder votre contenu avec Media Encoder Standard.

1.  Dans la fenêtre **paramètres** , sélectionnez **actifs**.  
2.  Dans la fenêtre **actifs** , sélectionnez l’élément que vous souhaitez coder.
3.  Appuyez sur le bouton **Encoder** .
4.  Dans la fenêtre **Encoder un bien** , sélectionnez le processeur « Standard Media Encoder » et un prédéfini. Par exemple, si vous connaissez votre vidéo d’entrée avec une résolution de 1920 x 1080 pixels, vous pouvez utiliser le « H264 débit plusieurs 1080p « prédéfinis. Pour plus d’informations sur les présélections, voir [cet](https://msdn.microsoft.com/library/azure/mt269960.aspx) article –, il est important de sélectionner le paramètre prédéfini qui convient le mieux pour la saisie vidéo. Si vous avez une vidéo basse résolution (640 x 360), puis vous ne devez pas utiliser la valeur par défaut « H264 débit plusieurs 1080p « prédéfinis.
    
    Pour faciliter la gestion, vous avez une possibilité de modifier le nom de la ressource de sortie et le nom de la tâche.
        
    ![Encoder actifs](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Appuyez sur **créer**.

### <a name="monitor-encoding-job-progress"></a>Surveiller l’avancement de tâche codage

Pour surveiller l’avancement de la tâche de codage, cliquez sur **paramètres** (en haut de la page), puis sélectionnez **tâches**.

![Tâches](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publier du contenu

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

>[AZURE.NOTE] Si vous avez utilisé le portail pour créer des repères avant mars 2015, Locator dont la date d’expiration de deux ans ont été créés.  

Pour mettre à jour une date d’expiration sur un repère, utilisez [reste](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou API [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Lorsque vous mettez à jour la date d’expiration d’un repère associations de sécurité, les modifications de l’URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Pour utiliser le portail de publication d’un bien

Pour utiliser le portail pour publier un bien, procédez comme suit :

1. Sélectionnez **paramètres** > **actifs**.
1. Sélectionnez l’élément que vous voulez publier.
1. Cliquez sur le bouton **Publier** .
1. Sélectionnez le type de repère.
2. Appuyez sur **Ajouter**.

    ![Publier](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L’URL est ajoutée à la liste des **URL publié**.

## <a name="play-content-from-the-portal"></a>Lire le contenu à partir du portail

Le portail Azure fournit un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo souhaitée, puis sur le bouton **lecture** .

![Publier](./media/media-services-portal-vod-get-started/media-services-play.png)

Certaines considérations s’appliquent :

- Vérifiez que la vidéo a été publiée.
- Ce **lecteur multimédia** est lu à partir de la valeur par défaut diffusion en continu de point de terminaison. Si vous souhaitez lire à partir d’un point de terminaison de la diffusion en continu de ceux définis par défaut, cliquez sur pour copier l’URL et utiliser un autre lecteur. Par exemple, [Le lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


