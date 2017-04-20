<properties 
    pageTitle="Didacticiels convertisseur Media Encoder Premium flux de travail" 
    description="Ce document contient des procédures pas à pas qui expliquent comment effectuer des tâches avancées avec le flux de travail de Media Encoder Premium et également créer des flux de travail complexes avec le Concepteur de flux de travail." 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>Didacticiels de flux de travail Media Encoder Premium avancés

##<a name="overview"></a>Vue d’ensemble 

Ce document contient des procédures pas à pas expliquant comment personnaliser des flux de travail avec le **Concepteur de flux de travail**. Vous trouverez les fichiers de flux de travail réel [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

##<a name="toc"></a>TABLE DES MATIÈRES

Les rubriques suivantes :

- [Codage MXF dans un seul débit MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [Démarrer un nouveau flux de travail](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [À l’aide de l’entrée du fichier multimédia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [Inspecter des flux de données](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [Ajout d’un vidéo encodeur pour. Génération du fichier MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [Encodage du flux audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [MULTIPLEXAGE flux Audio et vidéo dans un conteneur MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [Enregistrer le fichier MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [Création d’une ressource de Services multimédia à partir du fichier de sortie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [Tester le flux de travail terminé localement](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [Codage MXF dans multibitrate MP4s - emballage dynamique activé](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [Ajout d’une ou plusieurs sorties MP4 supplémentaires](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [Configurer les noms de fichier de sortie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [Ajouter une piste Audio distincte](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [Ajout de la. Fichier SMIL ISM](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [Codage MXF dans multibitrate MP4 - plan améliorée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [Vue d’ensemble du flux de travail afin d’améliorer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [Conventions d’affectation de noms des fichiers](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [Propriétés du composant de publication sur la racine du flux de travail](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [Ont générés fichier de copie noms s’appuient sur les valeurs de propriété publiés](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [Ajout de miniatures à multibitrate sortie MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [Vue d’ensemble du flux de travail pour ajouter des miniatures à](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [Ajout de codage JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [Concernant la conversion de l’espace de couleurs](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [Écrire les miniatures](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [Détecter les erreurs dans un flux de travail](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [Flux de travail terminé](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [Ajustement de la temporelles de sortie multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [Vue d’ensemble du flux de travail pour commencer à ajouter ajustement](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [À l’aide du découpage de flux de données](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [Flux de travail terminé](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [Présentation du composant de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [L’écriture de script au sein d’un flux de travail : Bonjour tout le monde](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [Ajustement de la trame de sortie multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [Vue d’ensemble de plan pour commencer à ajouter ajustement](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [À l’aide de la liste des clips XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [Modification de la liste des images à partir d’un composant de script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [Ajout d’une propriété de commodité ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>Codage MXF dans un seul débit MP4
 
Dans cette procédure, nous allons créer un débit unique. Fichier MP4 avec HE-AAC codé audio à partir d’un. Fichier d’entrée MXF. 

###<a id="MXF_to_MP4_start_new"></a>Démarrer un nouveau flux de travail 

Ouvrir le Concepteur de flux de travail, puis sélectionnez « Fichier «- » nouvel espace de travail «- » transcoder plan » 

Le nouveau flux de travail s’afficheront 3 éléments : 

- Fichier Source principal
- Liste des clips XML
- Sortie fichier/actif  

![Nouveau flux de travail de codage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nouveau flux de travail de codage*

###<a id="MXF_to_MP4_with_file_input"></a>À l’aide de l’entrée du fichier multimédia

Pour accepter notre fichier multimédia d’entrée, une commence à l’ajout d’un composant Media fichier d’entrée. Pour ajouter un composant au flux de travail, recherchez-le dans la zone de recherche référentiel et faites glisser l’entrée souhaitée sur le volet de concepteur. Procédez comme suit pour le fichier multimédia d’entrée et reliez le fichier Source principal pour le code confidentiel d’entrée de nom de fichier à partir du fichier multimédia d’entrée.

![Entrée du fichier multimédia connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrée du fichier multimédia connecté*

Avant que nous pouvons faire beaucoup d’autre, nous devez tout d’abord indiquer vers le Concepteur de flux de travail quel fichier exemple nous aimerions à utiliser pour créer notre flux de travail avec. Pour ce faire, cliquez sur l’arrière-plan du volet de concepteur et recherchez la propriété du fichier Source principal dans le volet droit de propriété. Cliquez sur l’icône de dossier et sélectionnez le fichier souhaité pour tester le flux de travail avec. Dès que dans ce cas, le composant d’entrée du fichier multimédia inspecter le fichier et remplir des codes confidentiels sa sortie pour refléter le fichier qu'il inspecter.

![Entrée du fichier multimédia rempli](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrée du fichier multimédia rempli*

Tandis que cette option spécifie quels entrée nous souhaitons travailler, il n’indique pas encore où la sortie codée doit aller dans. Similaire à la façon dont le fichier Source principal a été configuré, maintenant configurer la propriété Variable de sortie dossier, juste en dessous.

![Propriétés d’entrée et sortie configurées](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriétés d’entrée et sortie configurées*

###<a id="MXF_to_MP4_streams"></a>Inspecter des flux de données

Il a souvent nécessaire de savoir comment le flux de recherche tel quel flux dans le flux de travail. Pour vérifier si un flux de données à tout moment dans le flux de travail, cliquez simplement sur un ou épingler d’entrée sur n’importe lequel des composants. Dans ce cas, essayez de cliquer sur le code confidentiel sortie vidéo non compressées à partir de notre entrée du fichier multimédia. Une boîte de dialogue s’ouvre qui permet à inspecter la vidéo sortante.

![Inspecter le code confidentiel sortie vidéo non compressées](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspecter le code confidentiel sortie vidéo non compressées*

Dans notre exemple, il nous indique par exemple que nous avons affaire à une entrée de 1920 x 1080 à 24 images par seconde 4:2:2 échantillonnages une vidéo de presque 2 minutes.

###<a id="MXF_to_MP4_file_generation"></a>Ajout d’un vidéo encodeur pour. Génération du fichier MP4

Notez que maintenant, une vidéo non compressées et plusieurs coins de sortie Audio compressées sont disponibles pour une utilisation sur notre entrée du fichier multimédia. Pour coder la vidéo entrante, nous devons un composant de codage - dans ce cas de génération. Fichiers MP4.

Pour encoder le flux vidéo à H.264, ajoutez le composant encodeur vidéo AVC à l’aire de conception. Ce composant prend un flux vidéo décompresser comme entrée et offre un flux vidéo compressé AVC sur son code confidentiel de sortie.

![Encodeur AVC non connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Encodeur AVC non connecté*

Ses propriétés déterminent comment exactement le codage se produit. Examinons certains des paramètres plus importants :

- Résultat de la largeur et la hauteur de sortie : ces fichiers déterminent la résolution de la vidéo encodée. Dans notre cas passons avec 640 x 360
- Fréquence d’images : lorsque la valeur relais il simplement adoptera la fréquence d’images source, il est possible de via la remplacer. Notez que cette conversion fréquence d’images n’est pas compensée en mouvement.
- Profil et niveau : ces fichiers déterminent le profil AVC et le niveau. Pour obtenir plus d’informations sur les différents niveaux et les profils, cliquez sur l’icône de point d’interrogation dans le composant encodeur vidéo AVC et la page d’aide s’affichera plus en détail chacun des niveaux. Notre exemple, passons avec profil principaux au niveau 3,2 (par défaut).
- Taux de contrôler le Mode et débit (kb/s) : dans notre scénario nous opter pour un débit constant (CBR) de sortie à 1200 kb/s
- Format vidéo : il s’agit du VUI (facilité d’utilisation des informations vidéo) qui obtient écrites dans le flux H.264 (informations côté qui peuvent être utilisés par un filtre pour améliorer l’affichage, mais pas essentielles à coder correctement) :
- NTSC (standard pour les États-Unis ou au Japon, à l’aide de 30 i/s)
- PAL (standard pour l’Europe, à l’aide de 25 images par seconde)
- Mode de taille de groupe d’images : nous allons configurer la taille de groupe d’images fixe notre fins avec un intervalle de clé de 2 secondes avec contigus fermé. Cela garantit la compatibilité avec emballage dynamique Azure Media Services fournit.

Pour flux notre encodeur AVC, connectez le code confidentiel sortie vidéo non compressées à partir du fichier d’entrée de média composant au code confidentiel de vidéo non compressées d’entrée à partir de l’encodeur AVC.

![Encodeur AVC connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Encodeur AVC principaux connecté*

###<a id="MXF_to_MP4_audio"></a>Encodage du flux audio

À ce stade, nous avons au format vidéo, mais le flux audio compressé d’origine reste encore à compresser. Pour cela, nous allons passer avec codage AAC par le composant encodeur AAC (Dolby). Ajoutez-le au flux de travail.

![Encodeur AVC non connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Encodeur AAC non connecté*

À présent une incompatibilité : il y n'a qu’un seul compressé audio d’entrée code confidentiel à partir de l’encodeur AAC pendant plus de probablement le fichier multimédia d’entrée aura deux différents compressées flux audio disponibles : une pour le canal audio gauche et l’autre pour la droite. (Si vous travaillez avec son d’ambiance, qui est 6 canaux.) Ainsi, il n’est pas possible de se connecter directement la partie audio de la source d’entrée du fichier multimédia dans l’encodeur audio AAC. Le composant AAC exige un flux audio « entrelacé » dit : un seul flux qui a gauche et les canaux droite interleaved entre eux. Une fois que nous savoir à partir de notre fichier multimédia source les pistes audio sont position dans la source, nous pouvons générer ce flux audio entrelacé avec la position des haut-parleurs correctement affectées pour gauche et droite.

Tout d’abord une souhaiterez généré un flux de données sont supprimé des canaux audio source requis. Le composant Audio flux ENTRELACEUR traitera pour nous. Ajouter au flux de travail et connecter des sorties audio à partir de l’entrée du fichier multimédia dedans.

![ENTRELACEUR connecté flux Audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*ENTRELACEUR connecté flux Audio*

Maintenant que nous disposons d’un flux audio entrelacé, nous vous n’avez pas encore préciser où vous souhaitez affecter la position des haut-parleurs de gauche ou la droite pour. Pour spécifier cela, nous pouvons exploiter l’assigne Position du présentateur.

![Ajout d’un assigne Position du présentateur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Ajout d’un assigne Position du présentateur*

Configurer l’assigne Position haut-parleur pour une utilisation avec un flux d’entrée en stéréo via un filtre prédéfini encodeur du canal prédéfinis appelé « 2.0 (L, R) » et « Personnalisés ». (Cela affecte la position de haut-parleur gauche à la chaîne 1 et la position de haut-parleur droit à canal 2.)

Connectez la sortie de l’assigne Position du présentateur à l’entrée de l’encodeur AAC. Ensuite, indiquez l’encodeur AAC à travailler avec un « 2.0 (L, R) » canal prédéfinis, afin qu’il sache de traiter audio en stéréo comme entrée.

###<a id="MXF_to_MP4_audio_and_fideo"></a>MULTIPLEXAGE flux Audio et vidéo dans un conteneur MP4

Étant donné notre AVC flux vidéo codé et notre AAC codés flux audio, nous pouvons capturer les deux dans un. Conteneur MP4. Le processus de combinaison de différents flux dans un seul est appelé « multiplexage » (ou « muxing »). Dans ce cas, nous allons entrelacement le flux audio et les vidéo dans un seul cohérente avec la. Package MP4. Le composant qui coordonnées pour un. Conteneur MP4 est appelé le multiplexage ISO MPEG-4. Ajouter un à l’aire de conception et connectez-vous à la fois l’encodeur vidéo AVC et l’encodeur AAC à ses entrées.

![Connecté MPEG4 multiplexage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Connecté MPEG4 multiplexage*

###<a id="MXF_to_MP4_writing_mp4"></a>Enregistrer le fichier MP4

Lors de la rédaction d’un fichier de sortie, le composant de sortie du fichier est utilisé. Nous pouvons nous connecter cela à la sortie de la multiplexage ISO MPEG-4 afin que le résultat est écrit sur le disque. Pour ce faire, connectez le code confidentiel sortie conteneur (MPEG-4) pour le code confidentiel d’écriture d’entrée de la sortie du fichier.

![Sortie du fichier de connexion](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Sortie du fichier de connexion*

Le nom de fichier qui est utilisé est déterminé par la propriété du fichier. Si cette propriété peut être codé en dur pour une valeur donnée, probablement un devoir définissez-la dans une expression à la place.

Pour que le flux de travail automatiquement détermine la sortie d’une expression de la propriété nom de fichier, cliquez sur le bouton en regard du nom de fichier (à côté de l’icône de dossier). Dans le menu déroulant, puis sélectionnez « Expression ». Cela fait apparaître l’éditeur d’expression. Effacer tout d’abord le contenu de l’éditeur.

![Éditeur d’Expression vide](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Éditeur d’Expression vide*

L’éditeur d’expression permet d’entrer une valeur littérale, mixte avec une ou plusieurs variables. Variables commencent par le signe dollar. Lorsque vous appuyez sur la touche $, l’éditeur affiche une zone de liste déroulante avec un éventail de variables disponibles. Dans notre exemple, nous allons utiliser une combinaison de la variable de répertoire de sortie et la variable de nom de fichier d’entrée de base :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Remplie à l’éditeur d’Expression](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Remplie à l’éditeur d’Expression*

>[AZURE.NOTE]Afin de voir voir un fichier de copie de votre travail codage dans Azure, vous devez fournir une valeur dans l’éditeur d’expression. 

Lorsque vous confirmez l’expression en appuyant sur OK, la fenêtre de propriété aperçu apparaît à la valeur que la propriété de fichier soit résolue à ce stade.

![Fichier Expression est résolue ; dir de sortie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Fichier Expression est résolue ; dir de sortie*

###<a id="MXF_to_MP4_asset_from_output"></a>Création d’une ressource de Services multimédia à partir du fichier de sortie

Tandis que nous avons écrit un fichier de sortie MP4, nous devons toujours indiquer que ce fichier appartient à l’actif de sortie qui généreront des services de support à la suite de l’exécution de ce flux de travail. À cet effet, le nœud de sortie fichier/actif dans la zone de dessin du flux de travail est utilisé. Tous les fichiers entrants dans ce nœud fera partie de l’actif Azure Media Services qui en résulte.

Reliez le fichier de sortie pour le composant de sortie fichier/actifs pour terminer le flux de travail.

![Flux de travail terminé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Flux de travail terminé*

###<a id="MXF_to_MP4_test"></a>Tester le flux de travail terminé localement

Pour tester le flux de travail localement, appuyez sur le bouton lecture dans la barre d’outils dans la partie supérieure. Une fois le flux de travail en cours d’exécution, vérifiez que la sortie générée dans le dossier de sortie configuré. Vous verrez le fichier de copie MP4 terminé qui a été codé à partir du fichier source d’entrée MXF.

##<a id="MXF_to_MP4_with_dyn_packaging"></a>Codage MXF dans MP4 - multibitrate emballage dynamique activé

Dans cette procédure pas à pas il nous allons de créer un jeu de plusieurs fichiers MP4 de débit avec AAC codé audio à partir d’une seule. Fichier d’entrée MXF.

Quand une sortie de biens multi-débit est vous souhaitez utiliser en combinaison avec les fonctionnalités d’emballage dynamique offertes par Azure Media Services, plusieurs fichiers MP4 aligné sur le groupe d’images de chaque un débit différente et la résolution doivent être générés. Pour ce faire, la procédure pas à pas [Le codage MXF dans un seul débit MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) nous fournit un bon point de départ.

![Démarrage du flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Démarrage du flux de travail*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Ajout d’une ou plusieurs sorties MP4 supplémentaires

Chaque fichier MP4 dans notre bien Azure Media Services qui en résulte prend en charge un débit différente et résolution. Nous allons ajouter un ou plusieurs fichiers de sortie MP4 au flux de travail.

Pour vous assurer que nous avons tous nos encodeur créé avec les mêmes paramètres, il est plus pratique dupliquer l’encodeur vidéo AVC déjà existants et configurer un autre combinaison de résolution et de débit (nous allons ajouter une de 960 x 540 à 25 images par seconde à 2,5 Mbps). Pour dupliquer l’encodeur existant, copie collez-la dans l’aire de conception.

Branchez le code confidentiel sortie vidéo non compressées de l’entrée du fichier multimédia dans notre nouveau composant AVC.

![Deuxième encodeur AVC connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Deuxième encodeur AVC connecté*

Maintenant adapter la configuration pour notre nouveau encodeur AVC sortir 960 x 540 à 2,5 Mbps. (Utilisez ses propriétés « largeur de la sortie », « Hauteur en sortie » et « Débit (kb/s) » pour cela.)

Étant donné nous voulons utilisation de la ressource qui en résulte avec emballage dynamique Azure Media Services, le point de terminaison de diffusion en continu doit être en mesure de génération à partir de ces fichiers MP4 fragments TLS/Fragmented MP4/tiret exactement alignés entre eux de sorte que les clients qui sont basculement entre différentes vitesses de transmission obtiennent une expérience audio et vidéo en continu lisse unique. Pour cela, nous avons besoin de vous assurer que, dans les propriétés des deux encodeurs AVC le groupe d’images (« groupe d’images ») taille pour les deux fichiers MP4 est définie sur 2 secondes, ce qui peut être effectuées à :

- définition du Mode de taille de groupe d’images à la taille de groupe d’images fixe et
- l’intervalle de cadre clé à deux secondes.
- également définir le contrôle IDR groupe d’images fermé pour vous assurer de groupe d’images toutes les sont permanent dans leurs propres sans dépendances

Pour rendre notre flux de travail pratique de comprendre, renommez l’encodeur AVC première de « encodeur vidéo AVC 640 x 360 kb/s 1200 » et l’encodeur AVC deuxième « encodeur vidéo AVC 960 x 540 2500 kb/s ».

Ajoutez maintenant une deuxième multiplexage ISO MPEG-4 et une deuxième sortie du fichier. Connectez le multiplexage à l’encodeur AVC nouveau et vérifiez que le résultat est dirigé vers la sortie du fichier. Puis connectez également d’entrée de la AAC décodage d’éléments audio sortie vers le nouveau de multiplexage. La sortie du fichier à son tour peut ensuite être connectée au nœud de sortie fichier/actif pour l’ajouter à la ressource de Services multimédia qui seront créés.

![Deuxième Muxer et sortie du fichier connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Deuxième Muxer et sortie du fichier connecté*

Compatibilité avec emballage dynamique Azure Media Services, configurer le Mode de bloc de multiplexage à compter du groupe d’images ou la durée et définir les groupes d’images par segment 1. (Elle doit être la valeur par défaut).

![Modes de segment Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modes de segment Muxer*

Remarque : vous souhaiterez peut-être répéter ce processus pour les autres combinaisons de débit et la résolution que vous souhaitez ajouter à la sortie de biens.

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurer les noms de fichier de sortie

Nous avons plusieurs seul fichier ajouté à la ressource de sortie. Cela permet de besoin Assurez-vous que les noms de fichiers pour chacun de ces fichiers sont différents et peut-être même appliquer une convention d’appellation afin qu’il devienne effacer du nom de fichier que vous avez affaire à.

Nom de fichier sortie peut être contrôlée à travers des expressions dans le concepteur. Ouvrir le volet de propriétés pour un des composants de sortie du fichier et ouvrez l’éditeur d’expression pour la propriété de fichier. Notre premier fichier de sortie a été configuré via l’expression suivante (voir le didacticiel pour allant [MXF à un seul débit sortie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)) :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Cela signifie que notre nom de fichier est déterminé par deux variables : le répertoire de sortie pour écrire et le nom de base du fichier source. Le premier groupe est exposé en tant que propriété à la racine du flux de travail et ce dernier est déterminé par le fichier entrant. Notez que le répertoire de sortie est ce que vous utilisez pour le test local ; Cette propriété sera remplacée par le moteur de flux de travail lorsque le flux de travail est exécutée par le processeur media sur le nuage dans Azure Media Services.
Pour donner à la fois nos fichiers de sortie un dénomination de sortie cohérente, modifier le premier fichier dénomination expression à :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

et le second à :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Exécuter un test intermédiaire exécuter pour vous assurer que les deux fichiers de sortie MP4 sont générées correctement.

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Ajouter une piste Audio distincte

Comme nous allons ultérieurement lorsque nous générer un fichier .ism à nos fichiers de sortie MP4, nous devez également disposer un fichier MP4 audio uniquement en tant que la piste audio de notre diffusion adaptative. Pour créer ce fichier, ajoutez une muxer supplémentaire pour le flux de travail (multiplexage ISO-MPEG-4) et connectez-vous code confidentiel de sortie de l’encodeur AAC avec son code confidentiel d’entrée pour le suivi des 1.

![Audio Muxer ajouté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer ajouté*

Créez un troisième composant de sortie de fichier pour le flux sortant à partir de la muxer de sortie et configurer la dénomination expression sous la forme de fichier :
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio Muxer création de sortie d’un fichier](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio Muxer création de sortie d’un fichier*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Ajout de la. Fichier SMIL ISM

Pour l’emballage dynamique travailler en combinaison avec à la fois MP4 fichiers (et la MP4 audio uniquement) dans notre bien Media Services, nous devons également un fichier manifeste (également appelé fichier « SMIL » : synchronisés multimédia Integration Language). Ce fichier indique à Azure Media Services quels fichiers MP4 sont disponibles pour emballage dynamique et sur ceux à prendre en compte pour la diffusion en continu audio. Un fichier manifeste par défaut pour un ensemble de MP4 avec un seul flux audio ressemble à ceci :
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

Le fichier .ism contient au sein d’une instruction switch, une référence à chacun des fichiers vidéo MP4 individuels et outre ces références de fichier audio également une (ou plus) pour un MP4 qui contient uniquement la partie audio de.

Génération du fichier manifest pour notre ensemble de MP4 peut être effectuée par un composant appelé le « Writer de manifeste AMS ». Pour l’utiliser, glissez vers la surface et connectez les coins de sortie « Rédiger complète » entre les trois composants de sortie du fichier et l’entrée AMS manifeste Writer. Vérifiez que pour vous connecter à la sortie du Writer AMS manifeste à la sortie fichier/actif.

À l’instar de nos autres composants de sortie de fichier, configurez le nom de sortie .ism fichier avec une expression :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Notre flux de travail terminé ressemble à la ci-dessous :

![MXF terminé au flux de travail multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF terminé au flux de travail multibitrate MP4*

##<a id="MXF_to__multibitrate_MP4"></a>Codage MXF dans multibitrate MP4 - plan améliorée

Dans la [procédure précédente de flux de travail](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) , nous avons vu comment un actif d’entrée MXF unique peut être converti en un bien sortie avec fichiers MP4 multi-débit, un fichier MP4 audio uniquement et un fichier manifest à utiliser conjointement avec emballage dynamique Azure Media Services.

Cette procédure pas à pas vous montrent comment quelques-uns des aspects peuvent être amélioré et apportées plus pratique.

###<a id="MXF_to_multibitrate_MP4_overview"></a>Vue d’ensemble du flux de travail afin d’améliorer

![Flux de travail Multibitrate MP4 pour améliorer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Flux de travail Multibitrate MP4 pour améliorer*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>Conventions d’affectation de noms des fichiers

Dans le flux de travail précédent nous spécifié une expression simple comme base pour générer les noms de fichier de sortie. Nous avons cependant des valeurs en double : toutes les les composants de fichier de sortie individuelle spécifiés telle expression.

Par exemple, notre composant de sortie de fichier pour le premier fichier vidéo est configuré avec cette expression :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Tandis que pour la deuxième sortie vidéo, nous avons une expression comme :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Serait-il aspirateur, moins source d’erreurs et plus pratique si nous avons évacuer certaines de cette duplication et rendre les choses plus configurable à la place ? Par chance nous pouvons : fonctionnalités d’expression valide du concepteur en combinaison avec la possibilité de créer des propriétés personnalisées sur notre racine du flux de travail nous donne un niveau supplémentaire de commodité.

Supposons que nous allons piloter configuration de nom de fichier à partir de vitesses de la transmission des fichiers MP4 individuels. Ces vitesses de transmission que nous allons visent à configurer dans un emplacement centralisé (à la racine de notre graphique), à partir de l’endroit où ils allez accessibles pour configurer et affaires de nom de fichier. Pour ce faire, nous allons commencer en le publiant la propriété de débit des codeurs AVC à la racine de notre flux de travail, afin qu’il devienne accessible depuis les deux la racine dans les encodeurs AVC. (Même si affichée dans deux quelque, il n'est qu’une seule valeur sous-jacente.)

###<a id="MXF_to__multibitrate_MP4_publishing"></a>Propriétés du composant de publication sur la racine du flux de travail

Ouvrez l’encodeur AVC première, accédez à la propriété de débit (kb/s) et dans le menu déroulant Choisissez publier.

![La propriété de débit de publication](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*La propriété de débit de publication*

Configurer la boîte de dialogue Publier sur Publier sur la racine de notre graphique de flux de travail, avec un nom publié de « video1bitrate » et un nom complet explicite de « Vidéo 1 débit ». Configurer un personnalisé nom du groupe appelé « Vitesses de diffusion en continu transmission » et appuyez sur Publier.

![La propriété de débit de publication](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Boîte de dialogue publication de propriété de débit*

Répétez cette opération pour la propriété de débit de l’encodeur AVC deuxième et nommez-la « video2bitrate » avec un nom d’affichage de « Vidéo 2 débit », dans le même groupe personnalisé « Vitesses de diffusion en continu transmission ».

Si nous inspecter maintenant les propriétés de la racine du flux de travail, nous verrons notre groupe personnalisé avec les deux propriétés publiées apparaissant. Les deux réfléchir les la valeur de leur débit encodeur AVC correspondante.

![Propriétés de débit publiés sur racine du flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Chaque fois que vous souhaitez accéder à ces propriétés à partir du code ou à partir d’une expression, nous pouvons le faire à ceci :

- à partir de code incorporé à partir d’un composant juste sous la racine : node.getPropertyAsString('.. / video1bitrate', null)
- dans une expression : ${ROOT_video1bitrate}
 
Nous allons terminer le groupe « Vitesses de diffusion en continu transmission » en le publiant notre débit piste audio dessus. Dans les propriétés de l’encodeur AAC, recherchez le paramètre de débit et sélectionnez Publier dans le menu déroulant en regard de celle-ci. Publier à la racine du graphique avec le nom « audio1bitrate » et afficher le nom « Audio 1 débit » au sein de notre groupe personnalisé « Vitesses de diffusion en continu transmission ».

![Boîte de dialogue publication de débit audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Boîte de dialogue publication de débit audio*

![Propriétés de vidéo et audio qui en résulte sur racine](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propriétés de vidéo et audio qui en résulte sur racine*

Notez que la modification d’un de ces trois valeurs également ré-configure et modifie les valeurs sur les composants respectifs, elles sont liées avec (et l’emplacement dans lequel publiées à partir de).

###<a id="MXF_to__multibitrate_MP4_output_files"></a>Ont générés fichier de copie noms s’appuient sur les valeurs de propriété publiés

Au lieu de coder nos noms de fichier généré, nous pouvons de maintenant modifier notre expression filename sur chacun des composants de sortie du fichier s’appuyer sur les propriétés de débit que nous vient d’être publiés sur la racine du graphique. À partir de notre première sortie du fichier, recherchez la propriété de fichier et modifier l’expression comme suit :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Les différents paramètres dans cette expression sont accessibles et entrés en appuyant sur le signe dollar sur le clavier dans la fenêtre de l’expression. Un des paramètres disponibles est notre propriété video1bitrate que nous avons publié précédemment.

![Pour accéder aux paramètres dans une expression](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Pour accéder aux paramètres dans une expression*

Procédez de même pour la sortie du fichier pour notre vidéo deuxième :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

et pour la sortie du fichier audio uniquement :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Si nous maintenant changer la vitesse de transmission pour tous les fichiers audio ou vidéo, l’encodeur correspondante à reconfigurer et la convention d’appellation en fonction de débit sera respectée tout automatique.

##<a id="thumbnails_to__multibitrate_MP4"></a>Ajout de miniatures à multibitrate sortie MP4

À partir d’un flux de travail génère [une multibitrate MP4 sortie à partir d’un MXF d’entrée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant l’examiner dans Ajout des miniatures à la sortie.

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>Vue d’ensemble du flux de travail pour ajouter des miniatures à

![Démarrer à partir de flux de travail Multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Démarrer à partir de flux de travail Multibitrate MP4*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Ajout de codage JPG

Cœur de notre génération miniature sera le composant encodeur JPG, en mesure de fichiers JPG de sortie.

![JPG encodeur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG encodeur*

Nous ne pouvons pas toutefois se connecter directement notre flux vidéo non compressées à partir de l’entrée du fichier multimédia dans l’encodeur JPG. Au lieu de cela, il souhaite remis cadres individuels. Cela, nous pouvons faire via le composant vidéo cadre grille.

![Connectez un portail de cadre à l’encodeur JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Connectez un portail de cadre à l’encodeur JPG*

Le portail de cadre toutes les autant secondes ou cadres permet une vidéo à passer. L’intervalle et l’heure de décalage avec lequel cela se produit peut être configuré dans les propriétés.

![Propriétés de la grille de cadre vidéo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriétés de la grille de cadre vidéo*

Nous allons créer une miniature de chaque minute en définissant le mode d’heure (secondes) et l’intervalle à 60.

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>Concernant la conversion de l’espace de couleurs

Tandis qu’il semblerait logique à la fois des codes confidentiels vidéo non compressées de la grille de cadre et l’entrée du fichier multimédia peuvent désormais être connectés, nous obtenez un message d’avertissement si nous ferait.

![Erreur d’espace couleur d’entrée](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erreur d’espace couleur d’entrée*

C’est parce que la façon dont les couleur informations sont représentées dans notre d’origine brute compressée flux vidéo, en provenance de notre MXF, est la différence entre l’encodeur JPG attend. Plus précisément, un dites « espace de couleurs « de « RVB » ou « En nuances de gris » est attendus dans. Cela signifie que le flux vidéo entrant du portail vidéo cadre devront dispose d’une conversion appliquée en ce qui concerne son espace de couleurs en premier.

Faites glisser sur le flux de travail le convertisseur espace couleur - Intel et connectez-la à notre porte cadre.

![Connexion d’un convertisseur espace couleur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Connexion d’un convertisseur espace couleur*

Dans la fenêtre Propriétés, sélectionnez l’entrée RVB 24 dans la liste prédéfini.

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Écrire les miniatures

Différent de notre vidéo MP4, le composant encodeur JPG génère plusieurs fichiers. Pour gérer cela, un composant de scène recherche JPG fichier Writer peut être utilisé : il sera tirer les miniatures JPG entrants et les tester, écrire chaque nom de fichier en cours suivie d’un nombre différent. (Nombre généralement indiquant le nombre de secondes/unités dans le flux de données tirée de la miniature).


![Présentation de l’enregistreur de fichier JPG scène recherche](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Présentation de l’enregistreur de fichier JPG scène recherche*

Configurer la propriété chemin d’accès au dossier de sortie avec l’expression : ${ROOT_outputWriteDirectory} 

et la propriété nom de fichier préfixe avec : 

    ${ROOT_sourceFileBaseName}_thumb_

Le préfixe permet de déterminer comment les fichiers miniatures sont en cours nommés. Ils seront suivi du suffixe avec un nombre indiquant la position du curseur dans le flux.


![Propriétés de recherche JPG fichier Writer scène](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriétés de recherche JPG fichier Writer scène*

Se connecter à l’enregistreur de fichier scène recherche JPG vers le nœud de sortie fichier/actif.

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>Détecter les erreurs dans un flux de travail

Se connecter à l’entrée du convertisseur espace couleur à la sortie vidéo compressée brute. Désormais effectuer un test local exécution du flux de travail. Il est probable le flux de travail est soudainement arrêter l’exécution et indiquer avec un plan rouge sur le composant qui a rencontré une erreur :

![Erreur du convertisseur espace couleur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erreur du convertisseur espace couleur*

Cliquez sur la petite icône « E » rouge dans le coin supérieur droit coin du composant convertisseur d’espace de couleurs pour voir quelle est la raison pour laquelle la tentative de codage a échoué.

![Boîte de dialogue couleur espace convertisseur d’erreur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Boîte de dialogue couleur espace convertisseur d’erreur*

Il s’avère, comme vous pouvez le voir, que l’espace de couleurs entrants standard pour le convertisseur espace couleur doit être rec601 de notre conversion demandée de YUV en RVB. Apparemment notre flux n’indique pas son rec601. (ENR 601 est une norme de codage entrelacés signaux vidéo analogiques sous forme de vidéo numérique. Il indique une région active détaillé 720 échantillons de luminance et 360 échantillons chrominance par ligne. La couleur système de codage est appelée YCbCr 4:2:2.)

Pour résoudre ce problème, nous allons indiquer sur les métadonnées de notre flux que nous avons affaire à contenu rec601. Pour ce faire, nous allons utiliser un composant de mise à jour de Type de données vidéo, nous allons placer entre nos source brute et le composant de conversion d’espace couleur. Cette mise à jour du type de données permet de propriétés de type pour la mise à jour manuelle de certaines données vidéo. Configurez-le pour indiquer un espace couleur Standard de « Enr 601 ». Cela entraîne la mise à jour de Type de données vidéo ajouter une balise le flux de données avec l’espace de couleurs « Enr 601 » s’il y a aucun espace couleur encore défini. (Elle ne remplace pas les métadonnées existantes, à moins que la case à cocher Ignorer a été activée.)

![Mise à jour Standard d’espace de couleur sur la mise à jour du Type de données](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Mise à jour Standard d’espace de couleur sur la mise à jour du Type de données*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>Flux de travail terminé

Maintenant que notre notre flux de travail est terminé, effectuez un autre test exécuter Voir passer.

![Flux de travail terminé pour la sortie multi-mp4 avec des miniatures](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Flux de travail terminé pour la sortie multi-mp4 avec des miniatures*

##<a id="time_based_trim"></a>Ajustement de la temporelles de sortie multibitrate MP4

À partir d’un flux de travail génère [une multibitrate MP4 sortie à partir d’un MXF d’entrée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant l’examiner dans réduction de la vidéo source en fonction de l’horodatage.

###<a id="time_based_trim_start"></a>Vue d’ensemble du flux de travail pour commencer à ajouter ajustement

![Démarrage du flux de travail Ajouter ajustement](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Démarrage du flux de travail Ajouter ajustement*

###<a id="time_based_trim_use_stream_trimmer"></a>À l’aide du découpage de flux de données

Le composant flux découpage permet de découper le début et la fin d’un flux d’entrée de base sur Minutage informations (secondes, minutes,...). Le découpage ne reconnaît pas filtrage basée sur images.

![Découpage de flux de données](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Découpage de flux de données*

Au lieu de liaison l’encodeurs AVC et l’assigne position de haut-parleur pour l’entrée du fichier multimédia directement, nous allons présenter entre celle le découpage de flux de données. (Un pour le signal vidéo et une pour le signal audio entrelacé).

![Placer le découpage de flux de données entre les deux](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Placer le découpage de flux de données entre les deux*

Nous allons configurer le découpage de sorte que nous traitera uniquement vidéo et audio entre 15 et 60 secondes dans la vidéo.

Accédez aux propriétés du découpage de flux vidéo et configurer les propriétés de l’heure de fin (60 s) et l’heure de début (15 s). Pour vous assurer à la fois notre découpage audio et vidéo est toujours configuré avec les mêmes début et fin valeurs, nous finaux publient ceux à la racine du flux de travail.

![Publier des propriétés au moment démarrer à partir de découpage de flux de données](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publier des propriétés au moment démarrer à partir de découpage de flux de données*

![Publier la boîte de dialogue Propriétés de l’heure de début](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publier la boîte de dialogue Propriétés de l’heure de début*

![Publier la boîte de dialogue Propriétés de l’heure de fin](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publier la boîte de dialogue Propriétés de l’heure de fin*


Si nous inspecter maintenant la racine de notre flux de travail, les deux propriétés sera affiché et configurable parfaitement à partir de là.

![Propriétés publiées disponibles sur racine](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriétés publiées disponibles sur racine*

Ouvrez les propriétés de filtrage du découpage audio et configurer les heures de début et de fin d’une expression qui fait référence aux propriétés publiées sur la racine de notre flux de travail.

Heure de début pour la partie audio de réduction :

    ${ROOT_TrimmingStartTime}

et pour son heure de fin :

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>Flux de travail terminé

![Flux de travail terminé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Flux de travail terminé*


##<a id="scripting"></a>Présentation du composant de script

Composants de script peuvent exécuter des scripts arbitraires pendant les phases d’exécution de notre flux de travail. Il existe quatre différents scripts pouvant être exécutées, chacune avec des caractéristiques spécifiques et leur emplacement dans le cycle de vie du flux de travail :

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

La documentation du composant script accède plus en détail pour chacun des éléments ci-dessus. Dans [la section suivante](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), le composant de script **realizeScript** est utilisé pour créer un fichier xml de liste de séquences à la volée lorsque le flux de travail démarre. Ce script est appelé pendant la configuration du composant, ce qui se passe une seule fois dans son cycle de vie.


###<a id="scripting_hello_world"></a>L’écriture de script au sein d’un flux de travail : Bonjour tout le monde

Faites glisser un composant de script sur l’aire de conception et renommez-le (par exemple, « SetClipListXML »).

![Ajout d’un composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Ajout d’un composant de script*

Lorsque vous examinez les propriétés du composant script, les quatre types différents de script sera affichée, chaque configurable à un script différent.

![Propriétés du composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriétés du composant de script*

Désactivez la processInputScript et ouvrez l’éditeur pour la realizeScript. Nous allons maintenant définissons et prêt à commencer l’écriture de script.

Scripts sont écrits en Groovy, un langage de script compilé dynamiquement pour la plateforme Java qui conserve la compatibilité avec Java. En fait, la plupart des Java code est valide une super.

Nous allons écrire un script une super du monde Bonjour simple dans le contexte de notre realizeScript. Dans l’éditeur, entrez les informations suivantes :

    node.log("hello world");

Exécutez maintenant une série de tests locale. Après cette exécution, inspecter la propriété journaux (via l’onglet système dans le composant script).

![Sortie du journal Hello world](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Sortie du journal Hello world*

L’objet de nœud que nous appelons la méthode journal, fait référence à notre « nœud « actuel ou le composant que nous allons l’écriture de script dans. Chaque composant possède la possibilité de données de journalisation de sortie, disponibles via l’onglet système. Dans ce cas, nous représentation littéral de chaîne « Bonjour tout le monde ». Important de comprendre ici est que cela peut s’avérer un outil précieux débogage, vous offre un aperçu de ce que le script est en fait.

À partir de notre environnement d’écriture de script, nous avons également avoir accès aux propriétés avec d’autres composants. Procédez comme suit :


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

Notre fenêtre journal permet de visualiser les éléments suivants :

![Sortie du journal pour accéder à des chemins d’accès de nœud](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Sortie du journal pour accéder à des chemins d’accès de nœud*


##<a id="frame_based_trim"></a>Ajustement de la trame de sortie multibitrate MP4

À partir d’un flux de travail génère [une multibitrate MP4 sortie à partir d’un MXF d’entrée](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant l’examiner dans réduction de la vidéo source basée sur le nombre de cadre.

###<a id="frame_based_trim_start"></a>Vue d’ensemble de plan pour commencer à ajouter ajustement

![Commencer à ajouter ajustement de flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Commencer à ajouter ajustement de flux de travail*

###<a id="frame_based_trim_clip_list"></a>À l’aide de la liste des clips XML

Dans tous les didacticiels de flux de travail précédente, nous avons utilisé le composant Media fichier d’entrée en tant que notre source d’entrée vidéo. Pour ce scénario spécifique, nous utiliserons le composant Source de la liste élément à la place. Notez qu’il s’agit pas de la meilleure façon de travail ; utiliser uniquement la Source de liste Clip lorsqu’il y a un motif réel pour le faire (comme dans l’en dessous de la casse, dans laquelle nous faisons utilisation des capacités de filtrage de liste clip).

Pour passer de notre entrée du fichier multimédia à la Source de liste d’images, faites glisser le composant Source de la liste sur l’aire de conception et connectez le code confidentiel Clip liste XML au nœud Clip liste XML du Concepteur de flux de travail. Cela doit remplir la Source de la liste images avec des codes confidentiels sortie, en fonction de nos entrées vidéo. À présent vous connecter les chevilles compressées vidéo et Audio compressées à partir de la la Source de liste Clip aux respectifs AVC encodeurs et ENTRELACEUR de flux Audio. Supprimer l’entrée du fichier multimédia.

![Remplacer l’entrée du fichier multimédia avec la liste Source](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Remplacer l’entrée du fichier multimédia avec la liste Source*

Le composant Source de la liste prend comme entrée un « élément liste XML ». Lorsque vous sélectionnez le fichier source pour le test localement, ce code xml liste clip est rempli automatiquement pour vous.

![Propriété de Clip liste XML rempli](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriété de Clip liste XML rempli*

Vous recherchez un peu plus le code xml, voici comment il ressemble à :

![Boîte de dialogue liste élément modifier](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Boîte de dialogue liste élément modifier*

Toutefois, cela ne reflète pas les fonctionnalités du clip liste xml. Une option que nous avons consiste à ajouter un élément « Trim » sous la source audio et vidéo, comme suit :

![Ajout d’un élément SUPPRESPACE à la liste des clips](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Ajout d’un élément SUPPRESPACE à la liste des clips*

Si vous modifiez le code xml liste clip ainsi ci-dessus et que vous effectuez un test local exécuter, vous verrez la vidéo correctement été rognée entre 10 et 20 secondes de la vidéo.

Contrairement à ce qui se passe lorsque vous effectuez une série locale Cependant, ce même très xml de liste de séquences aurait pas le même effet lorsqu’elle est appliquée dans un flux de travail s’exécute dans Azure Media Services. Azure Premium Encoder au démarrage, la liste de séquences xml est généré à chaque fois à nouveau, basé sur le fichier d’entrée, que la tâche de codage a été donnée. Cela signifie que les modifications sur le code xml seraient malheureusement être remplacées.

Pour corriger le code xml de liste de séquences en cours est réinitialisation quand une tâche de codage est démarrée, nous pouvons générer de nouveau il à la volée juste après le début de notre flux de travail. Ces actions personnalisées peuvent être effectuées à ce qui est appelé « Composant script ». Pour plus d’informations, voir [Présentation du composant de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).


Faites glisser un composant de script sur l’aire de conception et renommez-le « SetClipListXML ».

![Ajout d’un composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Ajout d’un composant de script*

Lorsque vous examinez les propriétés du composant script, les quatre types différents de script sera affichée, chaque configurable à un script différent.

![Propriétés du composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriétés du composant de script*


###<a id="frame_based_trim_modify_clip_list"></a>Modification de la liste des images à partir d’un composant de script

Avant de nouveau, nous pouvons écrire le code xml de liste de séquences qui est généré lors du démarrage de flux de travail, nous devez avoir accès à la liste de séquences xml propriété et le contenu. Nous pouvons le faire à ceci :

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Liste des clips entrants en cours d’enregistrement](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Liste des clips entrants en cours d’enregistrement*

Tout d’abord, nous devons déterminer à partir du point auquel jusqu’au point auquel vous souhaitez découper la vidéo. Pour effectuer cette opération pratique à l’utilisateur moins techniques du flux de travail, deux propriétés de publication à la racine du graphique. Pour ce faire, cliquez avec le bouton droit sur l’aire de conception et sélectionnez « Ajouter une propriété » :

- Première propriété : « ClippingTimeStart » du type : « Code temporel »
- Deuxième propriété : « ClippingTimeEnd » du type : « Code temporel »

![Ajouter la boîte de dialogue Propriétés pour l’heure de début de découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Ajouter la boîte de dialogue Propriétés pour l’heure de début de découpage*

![Publié détourage accessoires heure sur racine du flux de travail](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publié détourage accessoires heure sur racine du flux de travail*

Configurez les deux propriétés pour une valeur appropriée :

![Configurer le démarrage de découpage et terminez les propriétés](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurer le démarrage de découpage et terminez les propriétés*

À présent, à partir notre script, nous pouvons accéder à ces deux propriétés, comme suit :

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Fenêtre journal présentant un début et fin de la coupure](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Fenêtre journal présentant un début et fin de la coupure*

Nous allons analyser les chaînes de code temporel dans un plus pratique de l’écran, à l’aide d’une expression régulière simple :
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![Fenêtre journal avec sortie de code temporel analysée](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Fenêtre journal avec sortie de code temporel analysée*

Grâce à ces informations à portée de main, nous pouvons maintenant modifier le code xml de liste de séquences pour refléter les heures de début et de fin pour la capture précis souhaitée de la vidéo.

![Code de script pour ajouter des éléments SUPPRESPACE](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Code de script pour ajouter des éléments SUPPRESPACE*

Ceci permet à travers des opérations de manipulation de chaîne normale. Image clipart modifiée liste xml qui en résulte est écrites à la propriété clipListXML à la racine du flux de travail via la méthode « DéfinirPropriété ». La fenêtre du journal après l’exécution d’un autre test serait afficher les éléments suivants :

![Enregistrement de la liste des clips résultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Enregistrement de la liste des clips résultante*

Effectuez une série de tests pour voir comment les flux audio et vidéo ont été tronqués. Comme vous allez effectuer plusieurs tests avec des valeurs différentes pour les points de rognage, vous remarquerez que ceux ne seront pas tenu compte toutefois ! Le fait que le concepteur, contrairement au runtime Azure, ne remplace pas le fichier xml de liste de séquences chaque exécuter. Cela signifie qu’uniquement la première fois que vous avez défini les et de sortie points, entraînera le code xml transformer, tous les autres cas, notre clause de garde (si (clipListXML.indexOf («<trim>») == -1)) bloquera le flux de travail à partir de l’ajout d’un autre élément SUPPRESPACE lorsqu’il y a déjà une présenter.

Pour rendre notre flux de travail pratique tester localement, nous meilleures ajouter du code de maison conserver examine si un élément SUPPRESPACE était déjà présent. Si c’est le cas, nous pouvons la supprimer avant de poursuivre en modifiant le code xml avec les nouvelles valeurs. Plutôt que d’utiliser manipulations de chaînes ordinaire, il est probablement sûr effectuer cette opération via le modèle d’objet xml réel l’analyse.

Avant que nous pouvons ajouter ce type de code bien que nous aurez besoin d’ajouter un nombre d’instructions import au début de notre script tout d’abord :
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

Après cela, nous pouvons ajouter le code de nettoyage requis :

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
Ce code accède juste au-dessus du point auquel nous ajouter les éléments SUPPRESPACE à la liste de séquences xml.

À ce stade, nous pouvons exécuter et modifier notre flux de travail qu’autant que nous voulons tout en ayant les modifications soient appliquées jamais temps.    

###<a id="frame_based_trim_clippingenabled_prop"></a>Ajout d’une propriété de commodité ClippingEnabled

Peut-être pas toujours souhaitée réduction pour se produire, nous allons terminer notre flux de travail en ajoutant un indicateur booléen pratique qui indique ou non nous souhaitons activer réduction / détourage.

Comme avant, publiez une nouvelle propriété à la racine de notre flux de travail appelée « ClippingEnabled » de type « booléen ».

![Publier une propriété pour l’activation de découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publier une propriété pour l’activation de découpage*

Avec l’en dessous de la clause de garde simple, nous pouvons vérifier si le filtrage est requis et décider si notre liste des clips en tant que tel doit être modifié ou non.

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>Code complet

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>Voir aussi 

[Présentation de Premium codage dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Comment utiliser Premium codage dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md#media_encoder_premium_workflow)

[Formats de codec et Media Encoder Premium du flux de travail](media-services-premium-workflow-encoder-formats.md)

[Fichiers d’exemples de flux de travail](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Outil d’Azure Media Services Explorer](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
