<properties
    pageTitle="Comment rogner une vidéo | Microsoft Azure"
    description="Cet article vous explique comment rogner vidéos avec Media Encoder Standard."
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>Vidéos de rognage avec Media Encoder Standard

Vous pouvez utiliser Media Encoder Standard (MES) pour rogner votre entrée vidéo. Rognage est le processus de sélection d’une fenêtre rectangulaire dans le cadre de la vidéo et le codage uniquement les pixels dans cette fenêtre. Le diagramme suivant vous permet de montrer le processus.

![Rogner une vidéo](./media/media-services-crop-video/media-services-crop-video01.png)

Supposons que vous ayez comme entrée une vidéo qui dispose d’une résolution de 1920 x 1080 pixels (rapport 16:9), mais noires (zones piliers) à gauche et à droite, afin qu’uniquement une fenêtre de 4:3 ou 1440 x 1080 pixels contient des éléments vidéo active. Vous pouvez utiliser MES pour rogner ou modifier des noires et coder la région 1440 x 1080.

Rognage dans MES étant une étape de prétraitement, les paramètres de rognage dans le codage prédéfini s’appliquent à la vidéo d’entrée d’origine. Le codage est un stade ultérieur, et les paramètres hauteur/largeur s’appliquent à la *pré-mise traitées* vidéo et non à la vidéo d’origine. Lorsque vous concevez votre prédéfini vous devez procéder comme suit : (a) sélectionner les paramètres de rognage en fonction de la vidéo d’entrée d’origine et (b), sélectionnez votre Encoder paramètres en fonction de la vidéo rognée. Si vous ne correspondent pas à votre coder des paramètres pour la vidéo rognée, le résultat ne sera pas comme prévu.

La rubrique [suivante](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) montre comment créer une tâche de codage avec MES et comment spécifier un paramètre prédéfini de la tâche codage personnalisé. 

## <a name="creating-a-custom-preset"></a>Création d’un paramètre prédéfini

Dans l’exemple affiché dans le diagramme :

1. Entrée d’origine est de 1920 x 1080
1. Il doit être rognée vers une sortie de 1440 x 1080 est centré dans le cadre d’entrée
1. Cela signifie qu’un décalage X (1920 – 1440) / 2 = 240 et un Y décalage de zéro
1. La largeur et la hauteur du rectangle de rognage sont respectivement 1440 et 1080,
1. Dans la phase de codage, la poser consiste à produire des trois couches, sont résolutions 1440 x 1080, 960 x 720 et 480 x 360, respectivement

###<a name="json-preset"></a>JSON prédéfini


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


##<a name="restrictions-on-cropping"></a>Restrictions sur le rognage

La fonctionnalité de rognage est destinée à être manuelle. Vous devrez charger votre vidéo d’entrée dans un outil d’édition approprié qui vous permet de sélectionner des images d’intérêt, placez le curseur afin de déterminer le décalage pour le rectangle de rognage, afin de déterminer le codage prédéfini qui est réglé pour que des vidéos, etc. particulier. Cette fonctionnalité n’est pas destinée à activer des éléments tels que : détection automatique et la suppression de bordures de boîte aux lettres/pillarbox noir dans votre entrée vidéo.

Contraintes suivantes s’appliquent à la fonctionnalité de rognage. Si celles-ci ne sont pas remplies, la tâche Encoder peut échouer ou produire un résultat inattendu.

1. Les coordonnées et la taille du rectangle de rognage doivent correspondre au sein de la vidéo d’entrée
1. Comme indiqué ci-dessus, la largeur et hauteur dans les paramètres Encoder doivent correspondre à la vidéo rognée
1. Rognage s’applique aux vidéos capturés en mode paysage (c'est-à-dire non applicable à des vidéos enregistrées avec un smartphone maintenus verticalement ou en mode portrait)
1. Convient le mieux à une vidéo progressive capturée avec des pixels carrés

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Étape suivante
 
Voir Azure Media Services rubriques d’apprentissage professionnelles pour vous aider à en savoir plus sur les fonctionnalités proposées par AMS.  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
