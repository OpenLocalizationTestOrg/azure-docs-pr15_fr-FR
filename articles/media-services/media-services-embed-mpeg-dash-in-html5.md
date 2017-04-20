<properties 
    pageTitle="Incorporer une vidéo en continu Adaptive MPEG-tiret dans une Application HTML5 avec DASH.js | Microsoft Azure" 
    description="Cette rubrique montre comment incorporer une vidéo MPEG-tiret Adaptive diffusion en continu dans une Application HTML5 avec DASH.js." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporer une vidéo en continu Adaptive MPEG-tiret dans une Application HTML5 avec DASH.js

##<a name="overview"></a>Vue d’ensemble

MPEG-tiret est une norme ISO pour la diffusion adaptative de contenu vidéo, qui offre des avantages significatifs pour ceux qui souhaitent diffuser des vidéos de haute qualité, adaptive diffusion en continu de sortie. Avec MPEG-tiret, le flux vidéo va supprimer automatiquement à une définition inférieure lorsque le réseau devient saturé. Cela permet de réduire la probabilité que la visionneuse vois une vidéo « suspendue » tandis que le lecteur télécharge les secondes suivantes pour lire (également appelé tampon). Comme l’encombrement du réseau réduit, le lecteur vidéo retournera à son tour dans un flux de qualité supérieur. Cette possibilité d’adapter la bande passante requise entraîne également une heure de début plus rapide pour la vidéo. Cela signifie que quelques secondes peuvent être lus dans un segment qualité inférieur d’introduction pour télécharger et puis étape jusqu'à un contenu une fois suffisant qualité supérieur a été mis en mémoire tampon.

Dash.js est un lecteur de vidéo MPEG-tiret libres écrit en JavaScript. Son objectif est de proposer un lecteur robuste, disponibilité sur plusieurs plateformes qui peut être librement réutilisé dans les applications qui nécessitent la lecture de la vidéo. Il propose Lecture MPEG-tiret dans n’importe quel navigateur qui prend en charge le W3C Media Source Extensions (MSE), et aujourd'hui Chrome, Microsoft Edge et IE11 (autres navigateurs ont indiqué leur intention pour prendre en charge MSE). Pour plus d’informations sur DASH.js, js voir le référentiel dash.js GitHub.


##<a name="creating-a-browser-based-streaming-video-player"></a>Création d’un lecteur de vidéo en continu via un navigateur

Pour créer une page web simple qui affiche un lecteur vidéo avec attendus contrôles tels lecture, pause, rembobiner etc., vous devez :

1. Créer une page HTML
1. Ajoutez la balise vidéo
1. Ajouter le lecteur dash.js
1. Initialisation du lecteur
1. Ajouter un style CSS
1. Afficher les résultats dans un navigateur qui mettent en œuvre, MSE

Lors de l’initialisation du lecteur peut être effectuée dans quelques-unes des lignes de code JavaScript. À l’aide de dash.js, il vraiment est simple à incorporer une vidéo MPEG-tiret dans vos applications basé sur le navigateur.

##<a name="creating-the-html-page"></a>Création de la Page HTML

La première étape consiste à créer une page HTML standard qui contient l’élément **vidéo** , enregistrez ce fichier sous basicPlayer.html, comme l’illustre l’exemple suivant :

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>Ajout du lecteur DASH.js

Pour ajouter l’implémentation de référence dash.js à l’application, vous devez extraire le fichier dash.all.js à partir de la 1.0 version du projet dash.js. Il doit être enregistré dans le dossier JavaScript de votre application. Ce fichier est un fichier commodité qui regroupe tout le code dash.js nécessaires en un seul fichier. Si vous avez un coup de œil autour du référentiel dash.js, vous trouverez les fichiers individuels, code de test et bien plus encore, mais si vous voulez faire est dash.js utiliser, puis le fichier dash.all.js constitue la démarche.

Pour ajouter le lecteur dash.js à vos applications, ajoutez une balise de script à la section Administration centrale de basicPlayer.html :

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Ensuite, créez une fonction pour initialisation du lecteur lors du chargement de la page. Ajoutez le script suivant après la ligne dans lequel vous chargez dash.all.js :

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Cette fonction crée d’abord un DashContext. Il est utilisé pour configurer l’application pour un environnement d’exécution spécifique. À partir d’un point de vue technique, il définit les classes l’infrastructure d’injection de dépendance doit utiliser lors de la création de l’application. Dans la plupart des cas, vous allez utiliser Dash.di.DashContext.

Ensuite, instanciation de la classe principale de l’infrastructure dash.js, MediaPlayer. Ce cours contient les centrales méthodes requises tels que visionnez, placez le curseur, gère la relation avec l’élément vidéo et gère également l’interprétation du fichier multimédia Présentation Description (MPD) qui décrit la vidéo à lire.

La fonction startup() de la classe MediaPlayer est appelée pour s’assurer que le lecteur est prêt à lire la vidéo. Entre autres, cette fonctionnalité de sorte que toutes les classes nécessaires (telle que définie par le contexte) ont été chargés. Une fois que le lecteur est prêt, vous pouvez joindre l’élément vidéo à l’aide de la fonction attachView(). Cela permet le lecteur multimédia insérer la vidéo dans l’élément et également contrôler la lecture comme bon vous semble.

Transférer l’URL du fichier MPD vers le lecteur multimédia indiquer relatives à la vidéo qu'il est prévu à lire. La fonction setupVideo() venez de créer doit être exécuté une fois la page entièrement chargé. Procédez comme suit à l’aide de l’événement onload de l’élément de corps. Modifier votre <body> élément à :

    <body onload="setupVideo()">

Enfin, définissez la taille de l’élément vidéo à l’aide de feuilles CSS. Dans un environnement de diffusion en continu adaptive, il s’agit particulièrement important car la taille de la vidéo en cours de lecture peut changer en lecture s’adapte à l’évolution du réseau. Dans cette démonstration simple simplement forcer l’élément vidéo doit être 80 % de la fenêtre du navigateur disponibles en ajoutant le fichier CSS suivant à la section Administration centrale de la page :
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>Lecture d’une vidéo

Pour lire une vidéo, pointez le navigateur sur le fichier basicPlayback.html et cliquez sur Lire sur le lecteur vidéo affiché.


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[Développer des applications du lecteur vidéo](media-services-develop-video-players.md)

[GitHub dash.js référentiel](https://github.com/Dash-Industry-Forum/dash.js) 
