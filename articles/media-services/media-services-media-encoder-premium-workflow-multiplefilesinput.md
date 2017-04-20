<properties
    pageTitle="À l’aide de plusieurs fichiers d’entrée et propriétés du composant avec Premium Encoder | Microsoft Azure"
    description="Cette rubrique explique comment utiliser setRuntimeProperties pour utiliser plusieurs fichiers d’entrée et passer des données personnalisées au processeur média Media Encoder Premium flux de travail."
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>À l’aide de plusieurs fichiers d’entrée et propriétés du composant avec Encoder Premium

## <a name="overview"></a>Vue d’ensemble

Il existe des scénarios dans lesquels vous deviez pour personnaliser les propriétés du composant, spécifier le contenu d’une liste XML, ou envoyer plusieurs fichiers d’entrée lorsque vous envoyez une tâche avec le processeur média **Media Encoder Premium flux de travail** . Quelques exemples sont :

- Superposition de texte sur vidéo et la définition de la valeur de texte (par exemple, la date actuelle) en cours d’exécution pour chaque vidéo d’entrée.
- Personnaliser le code XML liste Clip (pour spécifier un ou plusieurs fichiers source, avec ou sans limitation, etc..).
- Superposant sur une image de logo sur l’entrée vidéo alors que la vidéo est codée.

Pour indiquer le **Flux de travail Media Encoder Premium** que que vous souhaitez modifier certaines propriétés dans le flux de travail lorsque vous créez la tâche ou envoyez plusieurs fichiers d’entrée, vous devez utiliser une chaîne de configuration qui contient **setRuntimeProperties** et/ou **transcodeSource**. Cette rubrique explique comment les utiliser.


## <a name="configuration-string-syntax"></a>Syntaxe des chaînes de configuration

La chaîne de configuration pour définir la tâche codage utilise un document XML qui ressemble à ceci :

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Voici le code c# qui lit la configuration XML à partir d’un fichier et passe à la tâche dans une tâche :

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>Personnalisation des propriétés du composant  

### <a name="property-with-a-simple-value"></a>Propriété avec une valeur simple
Dans certains cas, il est utile personnaliser une propriété de composant ainsi que le fichier de flux de travail doit être exécutée par le flux de travail Media Encoder Premium.

Supposons que vous avez créé un flux de travail ce texte superpositions sur vos vidéos, et le texte (par exemple, la date actuelle) est censée être définie au moment de l’exécution. Vous pouvez le faire en envoyant le texte à définir en tant que la nouvelle valeur pour la propriété text du composant superposition à partir de la tâche codage. Vous pouvez utiliser ce mécanisme pour modifier d’autres propriétés d’un composant dans le flux de travail (par exemple, la position ou la couleur de la superposition, la vitesse de décodage d’éléments AVC, etc..).

**setRuntimeProperties** permet de remplacer une propriété dans les composants du flux de travail.

Exemple :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>Propriété ayant une valeur XML

Pour définir une propriété qui attend une valeur XML, encapsuler à l’aide de `<![CDATA[ and ]]>`.

Exemple :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]N’oubliez pas ne pas de placer un chariot retour juste après `<![CDATA[`.


### <a name="propertypath-value"></a>valeur de propertyPath

Dans les exemples précédents, le propertyPath a été « / du fichier multimédia entrée/filename » ou « / inactiveTimeout » ou « clipListXml ».
En règle générale, c’est le nom du composant, puis le nom de la propriété. Le chemin d’accès peut avoir des niveaux plus ou moins, comme « / primarySourceFile » (parce que la propriété est à la racine du flux de travail) ou « / vidéo/opacité traitement/effets graphiques » (car la superposition est dans un groupe).    

Pour vérifier le nom du chemin d’accès et propriété, utilisez le bouton d’action qui se trouve immédiatement en regard de chaque propriété. Vous pouvez cliquez sur ce bouton action et sélectionnez **Modifier**. Cela vous montrent au nom réel de la propriété et immédiatement au-dessus, l’espace de noms.

![Action/Edit](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriété](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Plusieurs fichiers d’entrée

Chaque tâche que vous envoyez au **Flux de travail Media Encoder Premium** requiert deux éléments :

- Le premier est un *Flux de travail actifs* qui contient un fichier de flux de travail. Vous pouvez créer des fichiers de flux de travail à l’aide du [Concepteur de flux de travail](media-services-workflow-designer.md).
- Le second est une *Ressource multimédia* qui contient l’ou les fichiers multimédias que vous souhaitez coder.

Lorsque vous envoyez plusieurs fichiers multimédias à l’encodeur **Premium du flux de travail décodage d’éléments multimédia** , les contraintes suivantes s’appliquent :

- Tous les fichiers multimédias doivent être placé dans la même *Ressource multimédia*. À l’aide de plusieurs éléments multimédias n’est pas pris en charge.
- Vous devez définir le fichier primaire dans cette biens multimédias (dans l’idéal, il s’agit du fichier vidéo principal que l’encodeur est invité à traiter).
- Il est nécessaire de faire passer des données de configuration qui inclut l’élément **setRuntimeProperties** et/ou **transcodeSource** au processeur.
  - **setRuntimeProperties** est utilisé pour remplacer la propriété nom de fichier ou un autre dans les composants du flux de travail.
  - **transcodeSource** est utilisé pour spécifier le contenu d’une liste XML.

Connexions dans le flux de travail :

 - Si vous utilisez une ou plusieurs composants de l’entrée du fichier multimédia et plan à utiliser **setRuntimeProperties** pour spécifier le nom du fichier, puis ne pas y connecter le code confidentiel composant fichier principal. Assurez-vous qu’il n’existe aucune connexion entre l’objet fichier principal et les niveaux de fichier multimédia.
 - Si vous préférez utiliser une liste XML et un composant Source du média, puis vous pouvez connecter les deux ensemble.

![Aucune connexion entre le fichier Source principal et entrée du fichier multimédia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Il n’existe aucune connexion à partir du fichier principal ou aux composants de l’entrée du fichier multimédia si vous utilisez setRuntimeProperties pour définir la propriété nom de fichier.*

![Connexion à partir de la liste des clips XML à la Source de la liste de la bibliothèque](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Vous pouvez vous connecter Clip liste XML à la Source de média et utiliser transcodeSource.*


### <a name="clip-list-xml-customization"></a>Image de personnalisation XML de liste
Vous pouvez spécifier le code XML liste Clip dans le flux de travail en cours d’exécution à l’aide de **transcodeSource** dans la chaîne de configuration XML. Cette fonctionnalité nécessite le code confidentiel Clip liste XML être connecté au composant Source du média dans le flux de travail.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Si vous souhaitez spécifier /primarySourceFile pour utiliser cette propriété pour nommer les fichiers de sortie à l’aide de « Expressions », nous recommandons en passant le code XML liste Clip comme une propriété *une fois que* la propriété /primarySourceFile, pour éviter que la liste des clips être remplacé par le paramètre /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Avec filtrage précis supplémentaire :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>Exemple

Prenons un exemple dans lequel vous souhaitez superposer une image du logo de la vidéo, entrez alors que la vidéo est codée. Dans cet exemple, la vidéo d’entrée est appelée « MyInputVideo.mp4 » et le logo « MyLogo.png ». Vous devez effectuer les étapes suivantes :

- Créer un élément de flux de travail avec le fichier de flux de travail (voir l’exemple suivant).
- Créer une ressource multimédia, qui contient deux fichiers : MyInputVideo.mp4 comme fichier principal et MyLogo.png.
- Envoyer une tâche au flux de travail Media Encoder Premium media processeur avec les biens d’entrée ci-dessus et spécifiez la chaîne suivante de la configuration.

Configuration :

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


Dans l’exemple ci-dessus, le nom du fichier vidéo est envoyé le composant entrée du fichier multimédia et la propriété primarySourceFile. Le nom de fichier du logo est envoyé à une autre entrée de fichier multimédia qui est connecté au composant superposition graphique.

>[AZURE.NOTE]Le nom de fichier vidéo est envoyé à la propriété primarySourceFile. La raison consiste à utiliser cette propriété dans le flux de travail pour la création de l’utilisation d’Expressions, par exemple le nom du fichier sortie correcte.


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>Création d’une étape par étape de flux de travail qui recouvre un logo en haut de la vidéo     

Voici les étapes pour créer un flux de travail prend deux fichiers en entrée : une vidéo et une image. Il recouvre alors l’image en haut de la vidéo.

Ouvrir **Le Concepteur de flux de travail** et sélectionnez **fichier** > **Nouvel espace de travail** > **Transcoder plan**.

Le nouveau flux de travail montre les trois éléments :

- Fichier Source principal
- Liste des clips XML
- Sortie fichier/actif  

![Nouveau flux de travail de codage](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nouveau flux de travail de codage*


Pour accepter le fichier multimédia d’entrée, commencez à l’ajout d’un composant Media fichier d’entrée. Pour ajouter un composant au flux de travail, recherchez-le dans la zone de recherche référentiel et faites glisser l’entrée souhaitée sur le volet de concepteur.

Ensuite, ajoutez le fichier vidéo à utiliser pour la conception de votre flux de travail. Pour ce faire, cliquez sur le volet d’arrière-plan dans le Concepteur de flux de travail et recherchez la propriété du fichier Source principal dans le volet droit de propriété. Cliquez sur l’icône de dossier et sélectionnez le fichier vidéo approprié.

![Fichier principal Source](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Fichier principal Source*


Ensuite, spécifiez le fichier vidéo dans le composant d’entrée du fichier multimédia.   

![Source d’entrée du fichier multimédia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Source d’entrée du fichier multimédia*


Dès que dans ce cas, le composant d’entrée du fichier multimédia inspecter le fichier et remplir des codes confidentiels sa sortie pour refléter le fichier il inspecter.

L’étape suivante consiste à ajouter un « vidéo données Type de mise à jour » pour spécifier l’espace de couleurs à Rec.709. Ajouter un « vidéo Format convertisseur » qui est défini sur le type de données mise en page/disposition = Configurable plane. Cela va convertir le flux vidéo dans un format qui peut être considéré comme une source du composant superposition.

![mise à jour du Type de données et convertisseur de Format de vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Mise à jour du Type de données vidéo et convertisseur de Format*

![Type de disposition = Configurable plane](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Type de disposition est Configurable plane*

Ensuite, ajoutez un composant superposition vidéo et se connecter le code confidentiel vidéo (non compressé) à l’axe vidéo (non compressée) de l’entrée du fichier multimédia.

Ajouter une autre entrée du fichier multimédia (pour charger le fichier du logo), cliquez sur ce composant et renommez-le « Media fichier entrée Logo », puis sélectionnez une image (un fichier .png par exemple) dans la propriété de fichier. Connecter le code confidentiel images non compressées à l’axe compressées image de la superposition.

![Source du fichier superposition composant et image](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Source du fichier superposition composant et image*


Si vous souhaitez modifier la position du logo sur la vidéo (par exemple, vous souhaiterez positionner 10 pour cent sur le coin supérieur gauche de la vidéo), désactivez la case à cocher « Entrée manuelle ». Vous pouvez procéder ainsi car vous utilisez un fichier multimédia d’entrée pour fournir le fichier du logo au composant superposition.

![Position superposition](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Position superposition*


Pour encoder le flux vidéo à H.264, ajoutez les composants de décodage d’éléments encodeur vidéo AVC et AAC à l’aire de conception. Connecter les chevilles.
Configurer l’encodeur AAC, puis sélectionnez Format de l’Audio Conversion/prédéfini : 2.0 (L, R).

![Encodeurs audio et vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Encodeurs audio et vidéo*


Maintenant ajouter les composants **ISO Mpeg-4 multiplexage** et **Sortie du fichier** et connecter les chevilles comme indiqué.

![MP4 multiplexage et sortie du fichier](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexage et sortie du fichier*


Vous devez définir le nom du fichier de sortie. Cliquez sur le composant de **Sortie du fichier** et modifier l’expression pour le fichier :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nom du fichier de sortie](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nom du fichier de sortie*

Vous pouvez exécuter le flux de travail localement pour vérifier qu’il s’exécute correctement.

Une fois terminé, vous pouvez l’exécuter dans Azure Media Services.

Tout d’abord, préparer un bien dans Azure Media Services avec deux fichiers dedans : le fichier vidéo et le logo. Vous pouvez le faire en utilisant le .NET ou API REST. Vous pouvez également le faire à l’aide du portail Azure ou [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Ce didacticiel montre comment gérer les ressources avec AMSE. Il existe deux façons d’ajouter des fichiers à une ressource :

- Créer un dossier local, copiez les deux fichiers qu’il contient et faites glisser et déposez le dossier à l’onglet **actif** .
- Téléchargez le fichier vidéo en tant qu’actif, afficher les informations de biens, accédez à l’onglet fichiers et télécharger un fichier supplémentaire (logo).

>[AZURE.NOTE]Veillez à définir un fichier principal dans l’actif (le fichier vidéo principal).

![Fichiers de biens dans AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Fichiers de biens dans AMSE*


Sélectionnez l’élément et choisissez à coder avec Encoder Premium. Télécharger le flux de travail et sélectionnez-le.

Cliquez sur le bouton pour passer des données au processeur, puis ajoutez le code XML suivant pour définir les propriétés d’exécution :

![Encodeur Premium dans AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Encodeur Premium dans AMSE*


Ensuite, collez les données XML suivantes. Vous devez spécifier le nom du fichier vidéo pour l’entrée du fichier multimédia et la primarySourceFile. Spécifier le nom du nom de fichier pour le logo.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Si vous utilisez le Kit de développement .NET pour créer et exécuter la tâche, ces données XML doit être passé en tant que la chaîne de configuration.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Une fois la tâche est terminée, le fichier MP4 dans les biens de sortie affiche la superposition !

![Superposition de la vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Superposition de la vidéo*


Vous pouvez télécharger le flux de travail d’exemple à partir de [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## <a name="see-also"></a>Voir aussi

- [Présentation de Premium codage dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Comment utiliser le codage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Codage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Formats de flux de travail Media Encoder Premium et codecs](media-services-premium-workflow-encoder-formats.md)

- [Fichiers d’exemples de flux de travail](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Outil d’Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
