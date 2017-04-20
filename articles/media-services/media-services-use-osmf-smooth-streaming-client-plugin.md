<properties 
    pageTitle="Plug-in de diffusion en continu lisse pour l’infrastructure multimédia d’ouvrir la Source" 
    description="Découvrez comment utiliser le plug-in Azure Media Services Smooth Streaming pour Adobe Open Source Media Framework." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Comment utiliser le Smooth Streaming plug-in pour l’infrastructure multimédia d’ouvrir la Source Adobe est Microsoft

##<a name="overview"></a>Vue d’ensemble


Le plug-in Microsoft Smooth Streaming pour ouvrir Source Media Framework 2.0 (SS pour OSMF) étend les fonctionnalités par défaut du OSMF et ajoute la lecture du contenu Microsoft Smooth Streaming aux lecteurs OSMF nouveaux et existants. Le plug-in ajoute également des fonctionnalités de lecture Smooth Streaming à cet média lecture (SMP).

SS pour OSMF inclut deux versions du plug-in :

- Statique Smooth Streaming plug-in pour OSMF (.swc)

- Dynamique Smooth Streaming plug-in pour OSMF (.swf)

Ce document suppose que le lecteur a une connaissance générale du fonctionnement de OSMF et OSMF plug-ins. Pour plus d’informations sur OSMF, consultez la documentation sur le [site OSMF officiel](http://osmf.org/).

###<a name="smooth-streaming-plugin-for-osmf-20"></a>Plug-in de diffusion en continu lisse pour OSMF 2.0

Le plug-in prend en charge le chargement et la lecture du contenu de diffusion en continu lisse à la demande avec les fonctionnalités suivantes :

- Lecture Smooth Streaming à la demande (lecture, Pause, Seek, Stop)
- Lecture Live Smooth Streaming (lecture)
- Live fonctions DVR (Pause, Seek, lecture DVR, déplacement de vie)
- Prise en charge des codecs vidéo - H.264
- Prise en charge des codecs Audio - AAC
- Plusieurs audio langue avec l’API intégrés OSMF
- Sélection de la qualité de la lecture max avec API intégrés OSMF
- Side-car sous-titres avec le plug-in OSMF des légendes
- Adobe&reg; Flash&reg; Player 11,4 ou ultérieur.
- Cette version ne prend en charge OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Fonctionnalités prises en charge et les problèmes connus

Pour une liste complète des fonctionnalités prises en charge, les fonctionnalités non prises en charge et les problèmes connus, reportez-vous à [ce document](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).


## <a name="loading-the-plugin"></a>Charger le plug-in
Plug-ins OSMF peuvent être chargées statique (au moment de la compilation) ou dynamiquement (au moment de l’exécution). Le plug-in Smooth Streaming au téléchargement OSMF inclut les versions dynamiques et statiques.

- Chargement statique : pour charger statique, un fichier de bibliothèque statique (SWC) est requis. Plug-ins statiques sont ajoutés comme une référence aux projets et publipostage à l’intérieur du fichier de sortie au moment de la compilation.

- Chargement dynamique : pour charger dynamiquement, un fichier (SWF) précompilé est requis. Plug-ins dynamiques sont chargées dans le runtime et non inclus dans la sortie du projet. (Sortie compilée) Plug-ins dynamiques peuvent être chargés à l’aide des protocoles HTTP et de fichier.

Pour plus d’informations sur chargement statique et dynamique, consultez la [page du plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)officiel.

###<a name="ss-for-osmf-static-loading"></a>SS pour le chargement statique OSMF
L’extrait de code ci-dessous montre comment charger le plug-in SS pour OSMF statique et lire une vidéo de base à l’aide de la classe OSMF MediaFactory. Avant d’inclure le SS pour les codes OSMF, assurez-vous que la référence de projet comprend le plug-in statique « MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc ».

```
package 
{
    
    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    
    
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        

        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }
    
        private function initMediaPlayer():void
        {
        
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            
            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}
```


###<a name="ss-for-osmf-dynamic-loading"></a>SS pour chargement dynamique OSMF

L’extrait de code ci-dessous montre comment charger le plug-in SS pour OSMF dynamiquement et lire un basic vidéo à l’aide de la classe OSMF MediaFactory. Avant d’inclure le SS pour les codes OSMF, copier le plug-in dynamique « MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf » au dossier du projet si vous souhaitez charger à l’aide du protocole de fichier, ou sous un serveur web pour charger HTTP. Il est inutile d’inclure « MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc » dans les références de projet.

 
{package
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;

    
    //Sets the size of the SWF
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        
        
        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }
        
        private function initMediaPlayer():void
        {
            
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}

##<a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Lecture du média stroboscopique avec le plug-in SS ODMF dynamique

Smooth Streaming du plug-in dynamique OSMF est compatible avec [Cet média lecture (SMP)](http://osmf.org/strobe_mediaplayback.html). Le SS OSMF du plug-in permet d’ajouter la lecture du contenu Smooth Streaming en SMP. Pour ce faire, copiez « MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf » sous un serveur web pour charge HTTP en utilisant les étapes suivantes :

1.  Accédez à la [page Configuration de la lecture du média cet](http://osmf.org/dev/2.0gm/setup.html). 
2.  Définissez la src sur une source de Smooth Streaming (par exemple, http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.  Apportez les modifications de configuration souhaitée, puis cliquez sur Aperçu et mise à jour.
 
    **Remarque** Votre serveur web content doit un crossdomain.xml valide. 
4.  Copiez et collez le code dans une page HTML simple à l’aide de votre éditeur de texte, comme dans l’exemple suivant :



        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



5. Ajouter un plug-in OSMF de diffusion en continu lisse pour le code incorporé et enregistrer.

        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>


6.  Enregistrer votre page HTML et publier sur un serveur web. Accédez à la page web publiée à l’aide de votre Flash favori&reg; lecteur activé navigateur Internet (Internet Explorer, Chrome, Firefox, etc.).
7.  Apprécier Smooth Streaming de contenu à l’intérieur d’Adobe&reg; Flash&reg; lecteur.

Pour plus d’informations sur le développement de OSMF général, voir la [page de développement OSMF](http://osmf.org/resources.html)officiel.

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Voir aussi

[Microsoft Adaptive diffusion en continu de plug-in pour mettre à jour OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 
