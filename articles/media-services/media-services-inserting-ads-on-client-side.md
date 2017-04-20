<properties 
    pageTitle="Insertion d’annonces sur le côté client | Microsoft Azure" 
    description="Cette rubrique explique comment insérer des annonces sur le côté client." 
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
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="inserting-ads-on-the-client-side"></a>Insertion d’annonces sur le côté client

Cette rubrique contient des informations sur la façon d’insérer divers types d’annonces sur le côté client.

Pour plus d’informations sur le support de sous-titres et ad fermé dans Live diffusion en continu de vidéos, voir [prise en charge fermé sous-titres et normes de point d’insertion d’Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

>[AZURE.NOTE] Azure Media Player ne prend pas en charge les annonces.

##<a id="insert_ads_into_media"></a>Insertion d’annonces dans votre support

Azure Media Services prend en charge pour l’insertion d’ad via la plate-forme Windows Media : structures de lecteur. Structures de lecteur avec prise en charge ad sont disponibles pour Windows 8, Silverlight, Windows Phone 8 et les appareils iOS. Chaque framework lecteur contient des exemples de code qui vous montre comment mettre en œuvre une application de lecteur. Il existe trois types différents d’annonces, que vous pouvez insérer dans votre liste d’éléments multimédia :.

- **Linéaire** – annonces plein cadre qui suspendre la vidéo principale.
- **Non linéaire** – annonces superposition qui s’affichent comme la lecture de la vidéo principale, généralement un logo ou une autre image statique placé dans le lecteur.
- **Le Guide** – annonces qui s’affichent en dehors du lecteur.

Annonces peuvent être placés à tout moment dans la chronologie de la vidéo principale. Vous devez indiquer le lecteur quand lire l’annonce et les annonces à lire. Pour cela, à l’aide d’un jeu de fichiers basé sur XML standards : vidéo Ad Service modèle (VAST), numérique vidéo plusieurs Ad sélection (VMAP), modèle de séquençage résumés multimédias (mat) et numérique vidéo du lecteur Ad Interface définition (VPAID). Fichiers vastes spécifient les annonces s’affichent. Fichiers VMAP spécifier à quel moment lire différentes annonces et contenir vaste XML. Fichiers mat sont un autre moyen d’annonces séquence peut comporter elle aussi vaste XML. Les fichiers VPAID définissent une interface entre le lecteur vidéo et l’annonce ou le serveur ad.

Chaque framework lecteur fonctionne différemment et chaque est traité dans sa propre rubrique. Cette rubrique décrit les mécanismes de base permettant d’insérer des annonces. Applications du lecteur vidéo demander des annonces à partir d’un serveur ad. Le serveur ad peut répondre de plusieurs façons :

- Renvoyer un fichier vaste
- Renvoyer un fichier VMAP (avec VAST incorporé)
- Renvoyer un fichier mat (avec VAST incorporé)
- Renvoyer un vaste fichier avec des annonces VPAID

 
###<a name="using-a-video-ad-service-template-vast-file"></a>À l’aide d’un fichier de modèle (VAST) Service Ad vidéo

Un fichier vaste spécifie quels ad ou des annonces à afficher. Le code XML suivant est un exemple d’un fichier pour une annonce linéaire grand :
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
L’annonce linéaire est décrite par le **<Linear>** élément. Il indique la durée de l’annonce, suivi des événements, cliquez sur à, cliquez sur suivi et un certain nombre de **<MediaFile>** éléments. Suivre des événements sont spécifiées dans les **<TrackingEvents>** élément et autoriser un serveur ad effectuer le suivi de différents événements qui se produisent pendant que vous affichez l’annonce. Dans ce cas, le début, milieu, complète et développer événements suivis. L’événement de démarrage se produit lorsque la publicité est affichée. L’événement milieu se produit lorsqu’au moins 50 % de chronologie de l’annonce a été consultée. L’événement complete se produit lorsque l’annonce a été exécuté à la fin. L’événement Expand se produit lorsque l’utilisateur développe le lecteur vidéo en mode plein écran. Clics sont spécifiés avec un **<ClickThrough>** élément au sein d’un **<VideoClicks>** élément et spécifie un URI à une ressource à afficher lorsque l’utilisateur clique sur la publicité. ClickTracking spécifié dans un **<ClickTracking>** élément, également dans le **<VideoClicks>** élément et spécifie une ressource de suivi à demander lorsque l’utilisateur clique sur la publicité par le lecteur. La **<MediaFile>** éléments spécifient des informations sur un codage spécifique d’une annonce. Lorsqu’il y a plusieurs **<MediaFile>** élément, le lecteur vidéo pouvez choisir le codage de meilleures pour la plateforme. 

Annonces linéaires peuvent être affichées dans un ordre spécifié. Pour ce faire, ajouter d’autres <Ad> éléments pour la VAST fichier et spécifiez l’ordre à l’aide de l’attribut séquence. L’exemple suivant illustre ceci :
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
Annonces non linéaires sont spécifiées dans une <Creative> élément également. L’exemple suivant montre un <Creative> élément décrivant une annonce non linéaire.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
La **<NonLinearAds>** élément peut contenir un ou plusieurs **<NonLinear>** éléments, chacun d'entre eux peut être décrite une annonce non linéaire. La **<NonLinear>** élément spécifie la ressource pour la publicité non linéaire. La ressource peut être une **<StaticResouce>**, un **<IFrameResource>**, ou un **<HTMLResouce>**.**<StaticResource>** Décrit une ressource non-HTML et définit un attribut creativeType qui spécifie le mode d’affichage de la ressource :

Image/gif, image/jpeg, image/png – la ressource est affichée dans un élément HTML **<img>** balise.

Application/x-javascript – la ressource est affiché dans une balise HTML <**script**>.

Application/x-shockwave-flash – la ressource est affiché dans un lecteur Flash.

**<IFrameResource>**Décrit une ressource HTML qui peut être affichée dans un IFrame. **<HTMLResource>**Décrit une partie du code HTML qui peut être inséré dans une page web. **<TrackingEvents>**spécifier les événements de suivi et de l’URI pour demander lorsque l’événement se produit. Dans cet exemple, les événements acceptInvitation et réduire sont suivies. Pour plus d’informations sur la **<NonLinearAds>** élément et ses enfants, voir IAB.NET/VAST. Notez que la **<TrackingEvents>** élément se trouve dans le** <NonLinearAds> ** élément plutôt que la **<NonLinear>** élément.

Annonces Companion définies au sein d’un <CompanionAds> élément. La <CompanionAds> élément peut contenir un ou plusieurs <Companion> éléments. Chaque <Companion> élément décrit une annonce le Guide et peut contenir un <StaticResource>, <IFrameResource>, ou <HTMLResource> qui sont spécifié dans la même façon que dans une annonce non linéaire. Un fichier vaste peut contenir plusieurs annonces le Guide et l’application lecteur peut choisir la publicité plus appropriée pour l’afficher. Pour plus d’informations sur VAST, voir [vaste 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>À l’aide d’une vidéo numérique plusieurs fichier de sélection (VMAP) Ad

Un fichier VMAP permet de spécifier quand des sauts d’ad, combien de temps chaque saut de ligne est, combien d’annonces peut être affichés dans un saut et quels types d’annonces peuvent être affichées pendant un saut. Opérations suivantes dans un exemple de fichier VMAP qui définit un saut unique ad :
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
Un fichier VMAP commence par une <VMAP> élément qui contient une ou plusieurs <AdBreak> éléments, chaque définition d’un saut d’ad. Chaque saut ad spécifie un type de saut, saut ID et décalage de temps. L’attribut breakType Spécifie le type de publicité qui peut être lu pendant la pause : linéaire, non linéaire, ou afficher. Afficher un plan annonces aux annonces companion vaste. Plusieurs types d’ad peut être indiquée dans une liste séparée par des virgules (sans espace). Le breakID est un identificateur facultatif pour l’annonce. La timeOffset Spécifie lorsque la publicité doit s’afficher. Il peut être spécifié dans une des façons suivantes :

1. Heure – au format hh : mm : ou.mmm où le fichier est millisecondes. La valeur de cet attribut spécifie l’heure de début de la chronologie de la vidéo au début du saut d’ad.
1. Pourcentage – au format n où n correspond au pourcentage de la chronologie de la vidéo à lire avant la lecture de l’annonce
1. Début/fin – indique qu’une annonce doit être affichée avant ou après l’affichage de la vidéo
1. Positionner – indique l’ordre des sauts de page ad lorsque le minutage de tous les sauts d’ad est inconnu, par exemple en continu. L’ordre de chaque saut ad est spécifié dans le format #n où n est un nombre entier ou égale à 1. 1 indique la publicité doit être lu dès que possible, 2 indique la publicité doit être lu dans les délais de deuxième et ainsi de suite.

Dans l’élément <**AdBreak**> peut être un seul élément <**ADsource par celle**>. L’élément <**ADsource par celle**> contient les attributs suivants :

1. ID – spécifie un identificateur pour la source ad
1. allowMultipleAds – une valeur booléenne qui indique si plusieurs annonces peuvent être affichées pendant la pause ad
1. followRedirects – valeur booléenne facultative qui spécifie si le lecteur vidéo doit respecter redirige au sein d’une réponse ad

L’élément <**ADsource par celle**> fournit le lecteur une réponse ad inline ou une référence à une réponse ad. Il peut contenir un des éléments suivants :

- <VASTAdData>Indique une réponse ad vaste est incorporée dans le fichier VMAP
- <AdTagURI>un URI qui fait référence à une réponse ad à partir d’un autre système
- <CustomAdData>-une arbitraire de chaîne qui représente une réponse non vaste

Dans cet exemple, une réponse ad en ligne est spécifiée avec un <VASTAdData> élément qui contient une réponse ad vaste. Pour plus d’informations sur les autres éléments, voir [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

L’élément <**AdBreak**> peut contenir un seul élément <**TrackingEvents**>. L’élément <**TrackingEvents**> permet de suivre le début ou la fin d’un saut d’ad ou si une erreur est survenue pendant la pause ad. L’élément <**TrackingEvents**> contient un ou plusieurs <**suivi**> éléments, chacun d'entre eux spécifie un événement de suivi et de suivi URI. Les événements de suivi possibles sont :

1. breakStart – effectue le suivi de début d’un saut d’ad
1. breakEnd – effectuer le suivi de l’achèvement d’un saut d’ad
1. erreur – effectue le suivi de message d’erreur s’est produite pendant la pause ad

L’exemple suivant montre un fichier VMAP qui spécifie le suivi des événements

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Pour plus d’informations sur l’élément <**TrackingEvents**> et ses enfants, consultez http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>À l’aide d’un résumé de médias séquençage fichier de modèle (MAST)

Un fichier MAST vous permet de spécifier des déclencheurs qui définissent lorsqu’une annonce est affichée. Voici un exemple de fichier MAST contenant des déclencheurs pour une annonce pellicule antérieur, une annonce médiane pellicule et une annonce postérieures à la pellicule.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

Un fichier MAST commence par une **<MAST>** élément qui contient une **<triggers>** élément. La <triggers> élément contient une ou plusieurs **<trigger>** éléments qui définissent quand une annonce doit être répétée. 

La **<trigger>** élément contient un **<startConditions>** élément qui spécifient quand une annonce doit commencer à lire. La **<startConditions>** élément contient une ou plusieurs <condition> éléments. Lors de chaque <condition> est vrai un déclencheur est lancé ou révoqué selon que la <condition> est contenue dans une **< startConditions**> ou **<endConditions>** élément respectivement. Lorsque plusieurs <condition> éléments sont présents, ils sont considérés comme un ou implicite, une condition ont pour résultat vrai entraînera le déclencheur commencer. <condition>éléments peuvent être imbriquées. Lorsque enfant <condition> éléments sont prédéfinies, ils sont considérés comme un opérateur AND implicite, toutes les conditions doivent avoir la valeur true pour le déclencheur commencer. La <condition> élément contient les attributs suivants qui définissent la condition : 

1. **type** – Spécifie le type de condition, événement ou la propriété
1. **nom** : le nom de la propriété ou l’événement à utiliser lors de l’évaluation
1. **valeur** – la valeur qui sera évaluée par rapport à une propriété
1. **opérateur** – l’opération à utiliser lors de l’évaluation : EQ (égal), NEQ (non égal), GTR (supérieur), GEQ (supérieur ou égal à), LT (inférieur à), LEQ (inférieur ou égal à), MOD (modulo)

**<endConditions>**contiennent également des <condition> éléments. Quand une condition est vrai le déclencheur est réinitialisé. La <trigger> élément contient également un <sources> élément qui contient une ou plusieurs <source> éléments. La <source> éléments définissent l’URI à la réponse ad et le type de réponse ad. Dans cet exemple, un URI est donné à une réponse vaste. 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>À l’aide de la définition de l’Interface Ad du lecteur vidéo (VPAID)

VPAID est une API permettant d’unités ad exécutable communiquer avec un lecteur vidéo. Cela permet d’expériences ad hautement interactif. L’utilisateur peut interagir avec la publicité et la publicité peut répondre aux actions réalisées par la visionneuse. Par exemple une annonce peut afficher des boutons qui permettent à l’utilisateur afficher plus d’informations ou une version supérieure de l’annonce. Le lecteur vidéo doit prendre en charge l’API VPAID et l’annonce exécutable doit implémenter l’API. Lorsqu’un lecteur demande qu'une annonce à partir d’un serveur ad le serveur peut répondre avec une grande réponse qui contient une annonce VPAID.

Une annonce exécutable est créée dans le code qui doit être exécutée dans un environnement d’exécution tels que Adobe Flash™ ou un code JavaScript pouvant être exécutées dans un navigateur web. Lorsqu’un serveur ad renvoie une réponse vaste contenant une annonce VPAID, la valeur de l’apiFramework attribut dans le <MediaFile> élément doit être « VPAID ». Cet attribut spécifie l’annonce contenue une annonce exécutable VPAID. L’attribut type doit indiquer le type MIME du fichier exécutable, par exemple « application/x-shockwave-flash » ou « application/x-javascript ». L’extrait de code XML suivant la <MediaFile> élément à partir d’une réponse vaste contenant une annonce exécutable VPAID. 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

Une annonce exécutable peut être initialisée à l’aide de la <AdParameters> élément dans le <Linear> ou <NonLinear> éléments dans une grande réponse. Pour plus d’informations sur la <AdParameters> élément, consultez la section [3.0 vaste](http://www.iab.net/media/file/VASTv3.0.pdf). Pour plus d’informations sur l’API VPAID, voir [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implémentation d’une Windows ou le lecteur Windows Phone 8 avec Ad prise en charge

La plateforme Microsoft Media : Player Framework pour Windows 8 et Windows Phone 8 contient une collection d’exemples d’applications qui montrent comment mettre en œuvre une application du lecteur vidéo à l’aide de l’infrastructure. Vous pouvez télécharger le cadre du lecteur et les exemples de [lecteur Framework pour Windows 8 et Windows Phone 8](https://playerframework.codeplex.com).

Lorsque vous ouvrez la solution Microsoft.PlayerFramework.Xaml.Samples, vous verrez un nombre de dossiers au sein du projet. Le dossier publicitaires contient l’exemple de code pertinent pour la création d’un lecteur vidéo avec prise en charge Active Directory. À l’intérieur de la publicité dossier est un nombre de fichiers XAML/SC chaque qui montrent comment insérer des annonces dans une autre façon. La liste suivante décrit chacun :

- AdPodPage.xaml montre comment afficher un bloc ad.
- AdSchedulingPage.xaml montre comment planifier des annonces.
- FreeWheelPage.xaml montre comment utiliser le plug-in FreeWheel pour planifier des annonces.
- MastPage.xaml montre comment planifier des annonces avec un fichier mat.
- ProgrammaticAdPage.xaml montre comment planifier par programme des annonces en vidéo.
- ScheduleClipPage.xaml montre comment planifier une annonce sans un fichier vaste.
- VastLinearCompanionPage.xaml montre comment insérer un linéaire et le guide ad.
- VastNonLinearPage.xaml montre comment insérer une annonce non linéaire.
- VmapPage.xaml montre comment spécifier des annonces avec un fichier VMAP.

Chacun de ces exemples utilise la classe MediaPlayer définie par l’infrastructure de lecteur. La plupart des exemples utilisent des plug-ins qui prendre en charge les différents formats de réponse ad. L’exemple ProgrammaticAdPage par programme interagit avec une instance de MediaPlayer.

###<a name="adpodpage-sample"></a>Exemple de AdPodPage

Cet exemple utilise la AdSchedulerPlugin pour définir le moment afficher une publicité. Dans cet exemple, une annonce médiane pellicule est planifiée pour être lus après 5 secondes. Le bloc ad (un groupe d’annonces s’affichent dans l’ordre) spécifié dans un fichier vaste retourné par un serveur ad. L’URI vers le fichier grand spécifié dans le <RemoteAdSource> élément.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Pour plus d’informations sur la AdSchedulerPlugin, voir [publicité dans le cadre du lecteur sur Windows 8 et Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

Cet exemple utilise également la AdSchedulerPlugin. Elle planifie trois annonces, une annonce pellicule avant, une annonce médiane pellicule et une annonce postérieures à la pellicule. URI à la VAST pour chaque annonce spécifié dans un <RemoteAdSource> élément.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

Cet exemple utilise la FreeWheelPlugin qui spécifie un attribut Source qui spécifie un URI qui pointe vers un fichier SmartXML qui spécifie le contenu des annonces, ainsi que les informations de planification.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

Cet exemple utilise la MastSchedulerPlugin qui vous permet d’utiliser un fichier mat. L’attribut Source spécifie l’emplacement du fichier MAST.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

Cet exemple par programme interagit avec le lecteur multimédia. Le fichier ProgrammaticAdPage.xaml instancie le lecteur multimédia :

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

Le fichier ProgrammaticAdPage.xaml.cs crée un AdHandlerPlugin, ajoute un TimelineMarker pour spécifier quand une annonce doit être affichée, puis ajoute un gestionnaire pour l’événement MarkerReached qui charge un RemoteAdSource spécifier un URI vers un fichier vaste et puis lit l’annonce.
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

Cet exemple utilise la AdSchedulerPlugin pour planifier une annonce médiane pellicule en spécifiant un fichier .wmv contenant l’annonce.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

Cet exemple illustre comment utiliser la AdSchedulerPlugin une annonce linéaire médiane pellicule avec une annonce le Guide de planification. La <RemoteAdSource> élément spécifie l’emplacement du fichier vaste.
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

Cet exemple utilise la AdSchedulerPlugin pour planifier une linéaire et une annonce non linéaire. L’emplacement du fichier vaste est spécifié avec la <RemoteAdSource> élément.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

Cet exemple utilise la VmapSchedulerPlugin pour planifier des annonces à l’aide d’un fichier VMAP. L’URI vers le fichier VMAP spécifié dans l’attribut Source de la <VmapSchedulerPlugin> élément.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>Mise en œuvre d’un lecteur vidéo avec prise en charge Ad iOS


La plateforme Microsoft Media : Lecteur Framework pour iOS contient une collection d’exemples d’applications qui montrent comment mettre en œuvre une application du lecteur vidéo à l’aide de l’infrastructure. Vous pouvez télécharger le cadre du lecteur et les exemples [d’Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). La page github comporte un lien vers un site Wiki qui contient des informations supplémentaires sur le cadre du lecteur et présentation de l’échantillon du lecteur : [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).


###<a name="scheduling-ads-with-vmap"></a>Planifier des publicités avec VMAP

L’exemple suivant montre comment planifier des annonces à l’aide d’un fichier VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>Planifier des publicités avec VAST

L’exemple suivant montre comment planifier une annonce vaste liaison au plus tard.
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   L’exemple suivant montre comment planifier une annonce vaste liaison au plus tôt.
Exemple : 4 planifier au plus tôt liaison ad vaste //Download la VAST fichier, le cas ( ! [ framework.adResolver downloadManifest : & manifeste withURL : [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[logFrameworkError automatique] ;} else {adLinearTime.startTime = 7 ; adLinearTime.duration = 0 ;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

L’exemple suivant montre comment insérer une annonce à l’aide de grossière Couper modification (for)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier un bloc ad.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier une annonce médiane pellicule non pense-bête. Une annonce non pense-bête est lu uniquement une fois qu’indépendamment de toute recherche effectue la visionneuse.
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier une annonce médiane pellicule pense-bête. Une annonce pense-bête s’affiche chaque fois que le point dans la chronologie vidéo spécifié est atteint.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


L’exemple suivant montre comment planifier une annonce postérieures à la pellicule.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier une annonce pellicule avant.
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L’exemple suivant montre comment planifier une annonce médiane pellicule superposition.
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>Voir aussi

[Développer des applications du lecteur vidéo](media-services-develop-video-players.md)
