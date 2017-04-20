<properties 
    pageTitle="Notes de publication Media Services | Microsoft Azure" 
    description="Notes de publication de Services multimédia" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Notes de publication Azure Media Services

Ces notes de publication synthétisent les modifications provenant de versions précédentes et les problèmes connus.

>[AZURE.NOTE] Nous voulons entendre de nos clients et se concentrer sur la résolution des problèmes qui vous concernent. Pour signaler un problème ou poser des questions, publier sur le [Forum MSDN de Azure Media Services].


##<a id="issues"></a>Problèmes connus

### <a id="general_issues"></a>Problèmes d’ordre général Media Services

Problème|Description
---|---
Plusieurs en-têtes HTTP communs ne figurent pas dans l’API REST.|Si vous développez des applications de Services de support à l’aide de l’API REST, vous trouvez que certains champs d’en-tête HTTP courantes (y compris CLIENT-demande-ID, ID de demande et retour-CLIENT-demande-ID) ne sont pas pris en charge. Les en-têtes sont ajoutés dans une future mise à jour.
Encodage n’est pas autorisée.|Media Services utilise la valeur de la propriété IAssetFile.Name lors de la création d’URL pour le contenu de la diffusion en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Pour cette raison, encodage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères pour cent-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il peut uniquement être une «. » pour l’extension de nom de fichier.
La méthode ListBlobs qui fait partie de l’échoue Azure stockage SDK version 3.x.|Media Services génère des URL associations de sécurité en fonction de la version [12 / 02 / 2012](http://msdn.microsoft.com/library/azure/dn592123.aspx) . Si vous voulez utiliser Azure stockage SDK pour des objets BLOB de liste dans un conteneur blob, utilisez la méthode [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) qui fait partie de stockage Azure SDK version 2.x. La méthode ListBlobs qui fait partie de la version de stockage Azure SDK 3.x échouera.
Media Services la limitation mécanisme limite l’utilisation des ressources pour les applications qui vous demande un excès au service. Le service peut retourner le code d’état HTTP Service indisponible (503).|Pour plus d’informations, consultez la description du code d’état HTTP 503 dans la rubrique [Codes d’erreur Azure Media Services](http://msdn.microsoft.com/library/azure/dn168949.aspx) .
Lorsque vous interrogez entités, il est limité à 1000 entités retournées en même temps, car public reste v2 limite les résultats de requête dans les résultats de 1000. | Vous devez utiliser **Ignorer** et **prendre** (.NET) / **haut** (REST) comme décrit dans [Cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et [Cet exemple API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Certains clients peuvent provenir au sein d’un problème de balise répétition dans le manifeste Smooth Streaming.|Pour plus d’informations, consultez [cette](media-services-deliver-content-overview.md#known-issues) section.
Azure Media Services .NET SDK objets impossible et par conséquent ne fonctionnent pas avec la mise en cache Azure.|Si vous essayez de sérialiser l’objet AssetCollection Kit de développement pour l’ajouter à la mise en cache Azure, une exception est levée.
Tâches d’encodage échouent avec une chaîne de message « étape : DownloadFile. Code : System.NullReferenceException ».|Le flux de travail codage classique consiste à télécharger des fichiers vidéo d’entrée à une ressource d’entrée et envoyez une ou plusieurs tâches d’encodage pour cet actif d’entrée, sans modifier davantage que l’entrée actif. Toutefois, si vous modifiez l’actif d’entrée (par exemple en ajoutant, supprimant/renommer des fichiers au sein du bien), les travaux ultérieurs peuvent échouer avec une erreur DownloadFile. La solution consiste à supprimer l’élément d’entrée et télécharger à nouveau d’entrée (s) à un nouvel actif. 

##<a id="rest_version_history"></a>Historique de Version de l’API REST

Pour plus d’informations sur l’historique de version de l’API REST de Services de support, voir [Référence des API REST Azure Media Services].

##<a id="july_changes16"></a>Version juillet 2016

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Mises à jour au fichier manifeste (*. ISM) générés par le codage de tâches

Lorsqu’une tâche de codage est envoyée au Media Encoder Standard ou Azure Media Encoder, la tâche codage génère un [fichier manifeste en continu](media-services-deliver-content-overview.md) (* .ism) fichier dans la sortie actif. Avec la dernière version de service, la syntaxe de ce fichier manifeste diffusion en continu a été mis à jour.

>[AZURE.NOTE]La syntaxe de la diffusion en continu fichier manifeste (.ism) est réservée pour un usage interne et peuvent être modifiées dans les versions ultérieures. Veuillez ne modifiez pas ou manipuler le contenu de ce fichier.

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Un nouveau manifeste client (*. Fichier ISMC) est généré dans la sortie de biens lorsqu’une tâche de codage renvoie un ou plusieurs fichiers MP4

En commençant par la dernière version de service, après l’achèvement d’une tâche de codage qui génère une MP4 davantage de fichiers, la sortie bien contiendra également un fichier de manifeste (*.ismc) client en continu. Le fichier .ismc permet d’améliorer les performances de diffusion en continu dynamique. 

>[AZURE.NOTE]La syntaxe du fichier de manifeste (.ismc) client est réservée pour un usage interne et peuvent être modifiées dans les versions ultérieures. Veuillez ne modifiez pas ou manipuler le contenu de ce fichier.

Pour plus d’informations, voir [ce](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) billet de blog.

### <a name="known-issues"></a>Problèmes connus

Certains clients peuvent provenir de son adaptabilité à un problème de balise répétition dans le manifeste Smooth Streaming. Pour plus d’informations, consultez [cette](media-services-deliver-content-overview.md#known-issues) section.

##<a id="apr_changes16"></a>Version 2016 d’avril

### <a name="azure-media-analytics"></a>Azure Media Analytique

Azure Media Servces introduite Azure Media Analytique pour puissante intelligence vidéo. Pour plus d’informations, voir [Vue d’ensemble de Azure Media Services Analytique](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (Preview)

Azure Media Services maintenant vous permet de chiffrer dynamiquement votre HTTP Live Streaming (TLS) du contenu avec Apple FairPlay. Vous pouvez également utiliser le service de remise de licence AMS pour faire FairPlay des licences aux clients. Pour plus d’informations, voir [utiliser Azure Media Services pour transmettre en continu vos TLS du contenu protégé avec Apple FairPlay ](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Version de février 2016

La dernière version d’Azure Media Services SDK pour .NET (3.5.3) contient un correctif bogue connexe Widevine. Le problème a été : AssetDeliveryPolicy n’a pas pu être réutilisée pour plusieurs éléments chiffrés avec Widevine. Dans le cadre de ce correctif bogue la propriété suivante a été ajoutée au SDK : **WidevineBaseLicenseAcquisitionUrl**.
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>Version janvier 2016

Codage unités réservés renommé pour réduire la confusion des noms de décodage d’éléments.

Les unités réservées codage basique, Standard et Premium sont renommées et S1, S2, et S3 réservé unités, respectivement.  Les clients qui utilisent aujourd'hui RUs codage base verrez S1 comme étiquette dans Azure Portal (et, dans la facture), tout en Standard et Premium verront les étiquettes S2 et S3 respectivement. 

##<a id="dec_changes_15"></a>Version de décembre 2015

L’équipe Azure SDK publié une nouvelle version du package [Azure SDK pour PHP](http://github.com/Azure/azure-sdk-for-php) qui contient des mises à jour et les nouvelles fonctionnalités de Microsoft Azure Media Services. En particulier, le SDK Azure Media Services pour PHP prend désormais en charge les dernières fonctionnalités de [protection du contenu](media-services-content-protection-overview.md) : chiffrement dynamiques avec AES et DRM (PlayReady et Widevine) avec et sans restriction jeton. Il prend également en charge mise à l’échelle des [Unités de codage](media-services-dotnet-encoding-units.md).

Pour plus d’informations, voir :

- Le blog de [Microsoft Azure Media Services SDK pour PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
- Les [exemples de code](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) suivants pour vous aider à démarrer rapidement :
    - **vodworkflow_aes.php**: il s’agit d’un fichier PHP qui montre comment utiliser le chiffrement dynamique AES 128 et le Service de remise de clés. Il est basé sur l’échantillon .NET décrites dans [cet](media-services-protect-with-aes128.md) article.
    - **vodworkflow_aes.php**: il s’agit d’un fichier PHP qui montre comment utiliser le chiffrement dynamique PlayReady et Service de remise de licence. Il est basé sur l’échantillon .NET décrites dans [cet](media-services-protect-with-drm.md) article.
    - **scale_encoding_units.php**: il s’agit d’un fichier PHP qui montre comment mettre à l’échelle de codage unité réservée.


##<a id="nov_changes_15"></a>Version de novembre 2015

Azure Media Services propose désormais le service de remise de licence Google Widevine dans le cloud. Pour plus d’informations, reportez-vous à [ce billet de blog annonce](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). En outre, consultez [ce didacticiel](media-services-protect-with-drm.md) et [GitHub référentiel](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Notez que les services de remise de licence Widevine fournis par Azure Media Services est dans l’aperçu. Pour plus d’informations, voir [ce billet de blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Version octobre 2015

Azure Media Services (AMS) est désormais disponible dans les centres de données suivant : Brésil Sud, Inde ouest, Sud Inde et Inde Central. Vous pouvez maintenant utiliser le portail classique Azure pour [créer des comptes de Service de support](media-services-portal-create-account.md) et effectuer diverses tâches décrites [ici](https://azure.microsoft.com/documentation/services/media-services/). Toutefois, encodage Live n’est pas activée dans ces centres de données. En outre, pas tous les types de codage réservés unités sont disponibles dans ces centres de données.

- Brésil sud : Seuls Standard et unités réservés codage de base sont disponibles
- Inde ouest Inde centrale et du Sud Inde : uniquement le codage réservés unités de base sont disponibles


##<a id="september_changes_15"></a>Version de septembre 2015 

- AMS offre désormais la possibilité de protéger les vidéos à la demande (VOD) et les flux en direct avec la technologie DRM modulaire Widevine. Vous pouvez utiliser les partenaires de services de remise suivants pour vous aider à effectuer des licences Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Pour plus d’informations, voir [ce billet de blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

    Vous pouvez utiliser [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (en commençant par la version 3.5.1) ou API REST pour configurer votre AssetDeliveryConfiguration pour utiliser Widevine.  

- AMS désormais en charge les vidéos Apple ProRes. Vous pouvez maintenant télécharger vos fichiers de vidéos QuickTime source qui utilisent Apple ProRes ou autres codecs. Pour plus d’informations, voir [ce billet de blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Vous pouvez désormais utiliser Media Encoder Standard pour effectuer d’extraction archive découpage secondaire et en direct. Pour plus d’informations, voir [ce billet de blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Les mises à jour filtrage suivantes ont été apportées : 

    - Vous pouvez désormais utiliser format Apple HTTP Live Streaming (TLS) avec filtre audio uniquement. Cette mise à jour vous permet de supprimer piste audio uniquement en spécifiant (audio uniquement = false) dans l’URL.
    - Lorsque vous définissez des filtres pour vos ressources, vous avez désormais possibilité pour combiner plusieurs filtres (3) dans une seule URL.

    Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) billet de blog.

- AMS prend désormais en charge des trames I dans v4 TLS. Prise en charge de la trame I optimise opérations avance rapide et rembobiner. Par défaut, toutes les sorties v4 TLS incluent trame I sélection (EXT-X-I-FRAME-STREAM-INF).
 
    Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) billet de blog.

##<a id="august_changes_15"></a>Version août 2015

- Azure Media Services SDK pour Java V0.8.0 release et de nouveaux exemples sont désormais disponibles. Pour plus d’informations, voir :

    - [Billet de blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Référentiel d’exemples Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Mise à jour de lecteur multimédia Azure avec prise en charge des flux de données audio multiples. Pour plus d’informations, voir :
    - [Billet de blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Version juillet 2015

- Annonce de la disponibilité générale de Media Encoder Standard. Pour plus d’informations, voir [ce billet de blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

    Media Encoder Standard utilise Présélections décrites dans [cette](http://go.microsoft.com/fwlink/?LinkId=618336) section. Notez que lorsqu’à l’aide d’un paramètre prédéfini pour 4k code, vous devez obtenir le type d’unité **Premium** réservé. Pour plus d’informations, voir [comment le codage échelle](media-services-scale-media-processing-overview.md).
- Instantané des légendes en temps réel avec Azure Media Services et le lecteur. Pour plus d’informations, voir [ce billet de blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement logiciel .NET des Services multimédia

Azure Media Services .NET SDK est désormais la version 3.4.0.0. Les fonctionnalités suivantes a été ajoutée dans cette version :  

- Prise en charge implémenté une archive en ligne. Notez que vous ne pouvez pas télécharger un bien qui contient une archive en ligne.
- Prise en charge mise en œuvre des filtres dynamiques.
- Fonctionnalité implémentée qui permet aux utilisateurs de conserver un conteneur de stockage lors de la suppression des biens.
- Correctifs liés tentative de stratégies de canaux.
- Activé le **flux de travail Media Encoder Premium**.

##<a id="june_changes_15"></a>Version de juin 2015

###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement logiciel .NET des Services multimédia

Azure Media Services .NET SDK est désormais la version 3.3.0.0. Les fonctionnalités suivantes a été ajoutée dans cette version :  

- support technique de détection de se connecter OpenId,
- prise en charge pour la gestion des survol clés sur côté de fournisseur d’identité. 

Si vous utilisez un fournisseur d’identité qui expose OpenID connecter un document de découverte (comme les fournisseurs suivants : Azure Active Directory, Google, Salesforce), vous pouvez demander à Azure Media Services pour obtenir des clés de signature pour validation du jeton JWT de OpenID spécifications de découverte de connexion. 

Pour plus d’informations, reportez-vous [à l’aide des touches Web Json à partir de la spécification de découverte OpenID se connecter pour fonctionner avec l’authentification des jetons JWT dans Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Version de mai 2015

Annonce les nouvelles fonctionnalités suivantes :

- [Un aperçu de Live codage avec Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Manifeste dynamique](media-services-dynamic-manifest-overview.md)
- [Un aperçu du processeur média Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Version avril 2015

 ###<a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

- [Annonce de lecteur multimédia Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- En commençant par Media Services reste 2.10, canaux sont configurés pour un protocole RTMP, d’acquisition sont créées avec principal et secondaire d’acquisition URL. Pour plus d’informations, voir [canal configurations d’acquisition](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Mises à jour Indexer de Media Azure
- Prise en charge de langue espagnole
- Nouveau format xml de configuration

Pour plus d’informations, voir [ce billet de blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement logiciel .NET des Services multimédia

Azure Media Services .NET SDK est désormais la version 3.2.0.0.

Voici quelques-uns des clients en mises à jour :

- **Nouveauté**: modifié **TokenRestrictionTemplate.Issuer** et **TokenRestrictionTemplate.Audience** doivent être de type chaîne.
- Mises à jour relatives à la création personnalisée réessayer stratégies.
- Correctifs liés au chargement/téléchargement de fichiers.
- La classe **MediaServicesCredentials** accepte à présent accès principal et secondaire contrôle du point de terminaison s’authentifier.



##<a id="march_changes_15"></a>Version de mars 2015

### <a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

- Media Services propose désormais intégration Azure CDN. Pour prendre en charge l’intégration, la propriété **CdnEnabled** a été ajoutée au **StreamingEndpoint**.  **CdnEnabled** peut être utilisé avec l’API REST depuis la version 2,9 (pour plus d’informations, voir [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** peut être utilisé avec le Kit de développement .NET depuis la version 3.1.0.2 (pour plus d’informations, voir [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Annonce de **flux de travail Media Encoder Premium**. Pour plus d’informations, voir [Présentation de Premium codage dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Version de février 2015

### <a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

L’API REST Media Services est désormais la version 2,9. À partir de cette version, vous pouvez activer l’intégration Azure CDN avec points de terminaison de la diffusion. Pour plus d’informations, voir [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Version de janvier 2015

### <a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

Annonce de disponibilité générale de protection du contenu avec chiffrement dynamiques. Pour plus d’informations, voir [Azure Media Services améliore la sécurité de diffusion en continu avec la technologie disponibilité général de DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement logiciel .NET des Services multimédia

Azure Media Services .NET SDK est désormais la version 3.1.0.1.

Cette version marqué le constructeur Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate par défaut comme obsolète. Le nouveau constructeur prend TokenType comme argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Version de décembre 2014

###<a name="general-media-services-updates"></a>Mises à jour des Services multimédia pour général

- Certaines mises à jour et les nouvelles fonctionnalités ont été ajoutées au processeur Azure indexeur multimédia. Pour plus d’informations, consultez [Notes Azure Media indexeur Version 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Ajouter une nouvelle API REST qui vous permet de mettre à jour le codage réservé unités : [EncodingReservedUnitType avec reste](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- CORS ajoutés prend en charge pour le service de remise clés.
- Améliorations des performances d’interrogation d’options de stratégie d’autorisation ont été apportées.
- Dans le centre de données Chine, l' [URL de remise de clé](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) est maintenant par client (comme dans les autres centres de données).
- Durée de cible ajoutée TLS automatique. Lors de la diffusion en continu live, TLS est toujours empaqueté dynamiquement. Par défaut, Media Services calcule automatiquement ratio d’emballage segment TLS (FragmentsPerSegment) en fonction de l’intervalle d’image clé (KeyFrameInterval), également appelées groupe d’images – groupe d’images, reçus à partir de l’encodeur Live. Pour plus d’informations, voir [utilisation de la diffusion Live Azure Media Services].
 
###<a name="media-services-net-sdk-updates"></a>Mises à jour du Kit de développement logiciel .NET des Services multimédia

- [Azure Media Services.NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) est désormais la version 3.1.0.0.
- Mise à niveau la dépendance SDK .net 4.5 .NET Framework.
- Ajouter une nouvelle API qui vous permet de mettre à jour les unités réservées codage. Pour plus d’informations, voir [mise à jour de Type d’unité réservé et augmenter le RUs codage à l’aide de .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Ajouté JWT (JSON Web jeton) prend en charge pour l’authentification des jetons. Pour plus d’informations, voir [l’authentification des jetons JWT dans Azure Media Services et le chiffrement dynamique](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Décalage relative ajouté pour BeginDate et ExpirationDate dans le modèle de licence PlayReady.


##<a id="november_changes_14"></a>Version de novembre 2014

- Media Services permet désormais ajouter un contenu actif Smooth Streaming (FMP4) via une connexion SSL. Pour acquisition via le protocole SSL, veillez à mettre à jour l’URL de réception en HTTPS.  Pour plus d’informations sur live diffusion en continu, voir [utilisation de la diffusion Live Azure Media Services].
- Notez que pour l’instant, vous ne pouvez pas d’acquisition un flux en direct RTMP via une connexion SSL.
- Vous pouvez aussi diffuser votre contenu via une connexion SSL. Pour ce faire, assurez-vous que votre URL de diffusion en continu commencent par HTTPS.
- Notez que vous pouvez diffuser uniquement via le protocole SSL si le point de terminaison de diffusion en continu à partir de laquelle vous présenter votre contenu a été créé après 10 septembre 2014. Si votre URL de diffusion en continu est basées sur les points de terminaison de diffusion en continu créées après 10 septembre, l’URL contient « streaming.mediaservices.windows.net » (le nouveau format). URL de diffusion en continu qui contiennent « origin.mediaservices.windows.net » (l’ancien format) ne prennent pas en charge SSL. Si votre URL est dans l’ancien format et que vous voulez être en mesure de diffuser en continu sur SSL, [créer un point de terminaison diffusion en continu](media-services-portal-manage-streaming-endpoints.md). URL créées en fonction de la nouvelle extrémité de diffusion en continu permet de diffuser votre contenu via le protocole SSL.

##<a id="october_changes_14"></a>Version d’octobre 2014

### <a id="new_encoder_release"></a>Services Media Encoder Release

Annonce de la nouvelle version de Media Services Azure Media Encoder. Avec les dernières actualités Azure Media Encoder sont uniquement facturées sortie Go, mais dans le cas contraire l’encodeur nouvelle fonctionnalité compatible avec l’encodeur précédent. Pour plus d’informations [Media Services tarifs Details]).

### <a id="oct_sdk"></a>Services de support .NET SDK 

Media Services SDK pour les Extensions .NET est désormais la version 2.0.0.3.

Media Services SDK pour .NET est désormais la version 3.0.0.8.

Les modifications suivantes ont été apportées :

- Refactorisation dans classes de stratégies de réessayer.
- Ajout de chaîne de l’agent utilisateur aux en-têtes de requête http.
- Ajout d’étape de génération nuget restaurer.
- Résolution des tests de scénario pour utiliser x509 certificat référentiel.
- Validation des paramètres de mise à jour de canal et diffusion en continu de fin.
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Nouveau référentiel GitHub à des exemples de Media Services hôte

Exemples se trouvent dans le [référentiel de GitHub exemples Azure Media Services](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Version de septembre 2014

Métadonnées Media Services reste devient version2.7. Pour plus d’informations sur les dernières mises à jour reste, voir [Référence des API REST Azure Media Services].

Media Services SDK pour .NET est désormais la version 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Annulation de modifications

* **Origine** a été renommé [StreamingEndpoint].
* Une modification dans le comportement par défaut lors de l’utilisation du **Portail classique Azure** de coder et de publier fichiers MP4.

Précédemment, lorsque l’aide du portail de classique Azure pour publier un élément vidéo de fichier unique MP4 une URL associations de sécurité serait créé (associations de sécurité URL vous autorise à télécharger la vidéo à partir d’un stockage d’objets blob). Pour l’instant, lorsque vous utilisez le portail classique Azure de coder et de publier un élément de vidéo MP4 à fichier unique, l’URL générée pointe vers un point de terminaison de la diffusion Azure Media Services.  Cette modification n’affecte pas MP4 vidéos téléchargées sur Media Services et publiées sans être chiffrés par Azure Media Services directement.

Pour l’instant, vous avez deux options suivantes pour résoudre le problème.

* Activer la diffusion en continu unités et utilisez emballage dynamique pour transmettre en continu du bien .mp4 comme une présentation de diffusion en continu lisse.

* Créer une url associations de sécurité pour télécharger (ou progressivement lire) le .mp4. Pour plus d’informations sur la création d’un repère associations de sécurité, consultez [Exécution de contenu].


### <a id="sept_14_GA_changes"></a>Nouvelles fonctionnalités/scénarios qui font partie d’une version de disponibilité générale

* **Processeur de média indexeur**. Pour plus d’informations, voir [L’indexation des fichiers multimédia avec Azure Media indexeur].

* L’entité [StreamingEndpoint] maintenant vous permet d’ajouter des noms de domaine personnalisé (hôte).

    Un nom de domaine personnalisé à utiliser comme le nom du point de terminaison de la diffusion en continu Media Services, vous devez ajouter les noms d’hôte personnalisés à votre point de terminaison de diffusion en continu. Utilisez la Media Services reste API ou kit de développement .NET pour ajouter les noms d’hôte personnalisé.
    
    Les considérations suivantes s’appliquent :
    
    * Vous devez être le propriétaire du nom de domaine personnalisé.
    
    * La propriété du nom de domaine doit être validée par Azure Media Services. Pour valider le domaine, créez un enregistrement CName qui mappe <MediaServicesAccountId>.<parent domain> pour verifydns. < mediaservices zone dns >. 
    
    * Vous devez créer un autre CName qui correspond au nom d’hôte personnalisé (par exemple, sports.contoso.com) au nom d’hôte de votre StreamingEndpont de Services de support (par exemple, amstest.streaming.mediaservices.windows.net).


    Pour plus d’informations, consultez la propriété **CustomHostNames** dans la rubrique [StreamingEndpoint] .

### <a id="sept_14_preview_changes"></a>Nouvelles fonctionnalités/scénarios qui font partie de la version préliminaire public

* Diffusion en continu aperçu instantané. Pour plus d’informations, voir [utilisation de la diffusion Live Azure Media Services].

* Service de remise clés. Pour plus d’informations, voir [l’aide de chiffrement dynamiques AES 128 et le Service de remise clés].

* Chiffrement dynamiques AES. Pour plus d’informations, voir [l’aide de chiffrement dynamiques AES 128 et le Service de remise clés].

* Service de remise de licences PlayReady. Pour plus d’informations, voir [à l’aide de chiffrement dynamiques PlayReady et Service de remise de licence].

* Chiffrement PlayReady dynamique. Pour plus d’informations, voir [à l’aide de chiffrement dynamiques PlayReady et Service de remise de licence].

* Modèle de licence PlayReady Services multimédia. Pour plus d’informations, voir [Vue d’ensemble du modèle de licence PlayReady Media Services].

* Diffusion en continu de stockage chiffré actifs. Pour plus d’informations, voir [Contenu stockage Streaming chiffré].

##<a id="august_changes_14"></a>Version d’août 2014

Lorsque vous encodez un bien, un bien sortie est émis à l’issue de la tâche de codage. Jusqu'à ce que cette version, Azure Media Services Encoder produites métadonnées sur les éléments de sortie. À partir de cette version, l’encodeur génère également des métadonnées sur les éléments d’entrée. Pour plus d’informations, consultez les rubriques de [Sortie] et les [Métadonnées d’entrée] .


##<a id="july_changes_14"></a>Version juillet 2014

Les solutions de bogues suivantes ont été apportées pour le Gestionnaire de Azure Media Services et chiffreur :

* Audio uniquement est lu lorsque transmuxing un bien une archive en ligne à HTTP Live Streaming – ce problème a été résolu et désormais les fonctionnalités audio et vidéo sont lus.

* Lors de la compression d’un bien à HTTP Live en continu et le chiffrement AES 128 bits enveloppe, les flux empaquetés ne sont pas lus sur les appareils Android : ce bogue a été résolu et le flux de package est lue sur les appareils Android qui prennent en charge HTTP Live diffusion en continu.

##<a id="may_changes_14"></a>Version de mai 2014

### <a id="may_14_changes"></a>Mises à jour des Services multimédia pour général

Vous pouvez désormais utiliser [Emballage dynamique] pour flux HTTP Live Streaming (TLS) v3. Flux TLS v3, ajoutez au format suivant le chemin d’accès de locator origine : * .ism/manifest(format=m3u8-aapl-v3). Pour plus d’informations, voir [Blog de Nick Drouin].

Emballage dynamique maintenant également prend en charge l’exécution de TLS (v3 et v4) chiffrées avec PlayReady basée sur Smooth Streaming statique chiffrées avec PlayReady. Pour plus d’informations sur la façon de chiffrer Smooth Streaming avec PlayReady, voir [flux lisse protéger avec PlayReady].

### <a name="may_14_donnet_changes"></a>Mises à jour du Kit de développement logiciel .NET des Services multimédia

Les améliorations suivantes sont incluses dans la version .NET SDK 3.0.0.5 Media Services :

* Optimiser la vitesse et résilience pour le téléchargement/téléchargement des biens multimédias.

* Améliorations de la nouvelle tentative logique et transitoires la gestion des exceptions : 

    * Logique de détection et réessayer d’erreur transitoires ont été améliorées pour les exceptions qui sont créées par interroger, enregistrer les modifications, chargement ou téléchargement de fichiers. 
    
    * Lors de l’obtention des Exceptions Web (par exemple, lors d’une demande de jetons ACS), vous remarquerez que les erreurs irrécupérables ignore plus vite maintenant.

Pour plus d’informations, voir [Réessayer logique dans le Kit de développement de Services de support pour .NET].

##<a id="april_changes_14"></a>Version de décodage d’éléments avril 2014

### <a name="april_14_enocer_changes"></a>Services Media Encoder mises à jour

* Prise en charge pour traiter les fichiers AVI créés à l’aide de l’éditeur non linéaire herbe Valley fonction, où la vidéo est légèrement compressé à l’aide d’un codec herbe Valley siège social/HQX. Pour plus d’informations, voir [Herbe Valley annonce la fonction 7 diffusion en continu via le Cloud].

* Prise en charge pour spécifier la convention d’appellation pour les fichiers générés par l’encodeur multimédia. Pour plus d’informations, voir [Contrôle Media Service encodeur sortie les noms de fichiers].

* Prise en charge pour les superpositions audio et/ou vidéo. Pour plus d’informations, voir [Création superpositions].

* Prise en charge pour assembler plusieurs segments vidéo. Pour plus d’informations, voir [La combinaison des Segments vidéo].

* Fixe un bogue lié au transcodage MP4s où l’audio a été codé avec MPEG-1 Audio layer 3 (aka MP3).


##<a id="jan_feb_changes_14"></a>Versions de janvier/février 2014

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services SDK .NET 3.0.0.1, 3.0.0.2 et 3.0.0.3

Les modifications dans 3.0.0.1 et 3.0.0.2 sont les suivantes :

* Fixe les problèmes liés à l’utilisation de requêtes LINQ avec des instructions OrderBy.

* Fractionner des solutions de test dans [GitHub] dans les tests par unité et appuient sur des scénarios.

Pour plus d’informations sur les modifications, voir : [mises à jour de Azure Media Services .NET SDK 3.0.0.1 et 3.0.0.2].

Les modifications suivantes ont été apportées dans 3.0.0.3 :

* Dépendances de stockage Azure mis à niveau pour utiliser la version 3.0.3.0. 

* Problème de compatibilité descendante fixe pour 3.0. *.* versions. 


##<a id="december_changes_13"></a>Version de décembre 2013

### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0

>[AZURE.NOTE] versions 3.0.x.x ne sont pas compatibles avec les versions 2.4.x.x.

La dernière version du Kit de développement Media Services est désormais 3.0.0.0. Vous pouvez télécharger le dernier Nuget ou obtenir les bits à partir de [GitHub].

En commençant par la version 3.0.0.0 Media Services SDK, vous pouvez réutiliser les jetons [Azure Active Directory Access Control Service (ACS)] . Pour plus d’informations, consultez la section « Réutilisation de jetons Service de contrôle de l’accès » dans la rubrique [connexion à Media Services avec le Kit de développement de Services de support pour .NET] .

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK Extensions 2.0.0.0

Les Extensions Azure Media Services .NET SDK est un ensemble de méthodes d’extension et les fonctions d’assistance qui seront Simplifiez votre code et le rendre plus facile à développer avec Azure Media Services. Vous pouvez obtenir les dernières bits à partir [d’Azure Media Services.NET SDK Extensions].

##<a id="november_changes_13"></a>Version de novembre 2013

### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK modifications

À partir de cette version, le Kit de développement de Services de support pour .NET gère les erreurs transitoires défaillance pouvant se produire lorsque vous faites appel à la couche de l’API REST Media Services.

##<a id="august_changes_13"></a>Version août 2013

### <a name="aug_13_powershell_changes"></a>Applets de commande PowerShell de Services de support inclus dans les outils du Kit de développement logiciel Azure

Les applets de commande PowerShell de Services de support suivantes sont maintenant incluses dans les [Outils de sdk azure].

* Get-AzureMediaServices 

    Par exemple, `Get-AzureMediaServicesAccount`.

* Nouvelle AzureMediaServicesAccount 

    Par exemple, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* Nouvelle AzureMediaServicesKey 

    Par exemple, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Supprimer AzureMediaServicesAccount 

    Par exemple, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Version de juin 2013

### <a name="june_13_general_changes"></a>Azure Media Services modifications

Les modifications mentionnées dans cette section sont mises à jour incluses dans les versions de juin 2013 Media Services.

* Possibilité de lier plusieurs comptes de stockage à un compte de Service de support. 

    StorageAccount
    
    Asset.StorageAccountName et Asset.StorageAccount

* Possibilité de mettre à jour Job.Priority. 

* Notification liés entités et aux propriétés : 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Tâche

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure Media Services .NET SDK modifications

Les modifications suivantes sont incluses en juin 2013 Media Services SDK libère. La dernière Media Services SDK est disponible sur GitHub.

* En commençant par la version 2.3.0.0, le prend en charge Media Services SDK liaison stockage plusieurs comptes à un compte Media Services. API suivantes prennent en charge cette fonctionnalité :
    
    Le type IStorageAccount.
    
    La propriété Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
    
    La propriété StorageAccount.
    
    La propriété StorageAccountName.
    
    Pour plus d’informations, voir [Gestion des ressources de Services multimédias entre plusieurs comptes de stockage].

* Notification des API associées. En commençant par la version 2.2.0.0 que vous avez la possibilité pour écouter les notifications de stockage Azure file d’attente. Pour plus d’informations, voir [Notifications relatives aux tâches de gestion des Media Services].
    
    La propriété Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
    
    Le type Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
    
    Le type Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
    
    Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
    
    Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
    
    Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dépendance sur le Client de stockage Azure SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dépendance par rapport à OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Version de décembre 2012

### <a name="dec_12_dotnet_changes"></a>Azure Media Services .NET SDK modifications

* IntelliSense : Ajouté manquante documentation Intellisense pour de nombreux types.

* Microsoft.Practices.TransientFaultHandling.Core : Fixe un problème à l’endroit où le Kit de développement avait toujours une dépendance à une ancienne version de cet assembly. Le Kit de développement maintenant fait référence à la version 5.1.1209.1 de cet assembly.

Solutions pour les problèmes rencontrés dans le Kit de développement de novembre 2012 :

* IAsset.Locators.Count : Ce nombre est désormais correctement signalé sur les nouvelles interfaces IAsset après la suppression de tous les repères.

* IAssetFile.ContentFileSize : Cette valeur est maintenant correctement définie après un téléchargement par IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize : Cette propriété peut être définie maintenant lors de la création d’un fichier actif. Il a été précédemment en lecture seule.

* IAssetFile.Upload(filepath) : Résolution d’un problème où cette méthode de téléchargement synchrone a été lever l’erreur suivante lors du chargement de plusieurs fichiers à l’actif. L’erreur était « serveur n’a pas pu authentifier la demande. Vérifiez que la valeur de l’en-tête d’autorisation est formée correctement, y compris la signature. »

* IAssetFile.UploadAsync : Résolution d’un problème où pas plus de 5 fichiers peuvent être téléchargés simultanément.

* IAssetFile.UploadProgressChanged : Cet événement est maintenant fourni par le Kit de développement.

* IAssetFile.DownloadAsync (chaîne, BlobTransferClient, ILocator, CancellationToken) : cette surcharge de méthode est maintenant fournie.

* IAssetFile.DownloadAsync : Résolution d’un problème où pas plus de 5 fichiers peuvent être téléchargés simultanément.

* IAssetFile.Delete() : Résolution d’un problème où l’appel de delete peut-être lever une exception si aucun fichier a été téléchargé pour la IAssetFile.

* Tâches : Fixe un problème où chaîne un « MP4 à tâche de flux lisse » avec une « tâche de Protection de PlayReady » à l’aide d’un modèle de tâche n’aurait pas créer toutes les tâches du tout.

* EncryptionUtils.GetCertificateFromStore() : Cette méthode n’est plus exception est une référence null en raison des échecs de trouver le certificat basé sur les problèmes de configuration de certificats.

##<a id="november_changes_12"></a>Version de novembre 2012

Les modifications mentionnées dans cette section ont été mises à jour incluses dans novembre 2012 (version 2.0.0.0) SDK. Ces modifications peuvent nécessiter de code écrite pour la version bêta juin 2012 SDK relâchez à modifier ou réécrits.

* Actifs
    
    IAsset.Create(assetName) est la fonction de la création de biens uniquement. IAsset.Create télécharge n’est plus les fichiers dans le cadre de l’appel de méthode. Utilisez IAssetFile pour le téléchargement.
    
    La méthode IAsset.Publish et la valeur d’énumération AssetState.Publish ont été supprimés à partir du Kit de développement de Services. Tout code qui dépend de cette valeur doit être ré-écrite.

* FileInfo

    Ce cours a été supprimé et remplacé par IAssetFile.

    IAssetFiles

    IAssetFile remplace FileInfo et a un comportement différent. Pour l’utiliser, instanciation de l’objet IAssetFiles, suivi de chargement d’un fichier à l’aide de Media Services SDK ou le Kit de développement de stockage Azure. Surcharges IAssetFile.Upload suivantes peuvent être utilisées :

    * IAssetFile.Upload(filePath) : Méthode synchrone qui bloque le thread et est recommandée uniquement lorsque le téléchargement d’un fichier unique.
    
    * IAssetFile.UploadAsync (chemin d’accès, blobTransferClient, locator, cancellationToken) : une méthode asynchrone. C’est le mécanisme de téléchargement par défaut. 

    Problème connu : à l’aide de cancellationToken bien annule le téléchargement ; Toutefois, l’état de l’annulation des tâches peut s’agir d’un nombre des États. Vous devez correctement intercepter et gérer des exceptions.

* Repères
    
    Les versions spécifiques à l’origine ont été supprimées. Le contexte de sa spécifique. Locators.CreateSasLocator (biens, accessPolicy) seront marqués déconseillées ou supprimées par GA. Consultez la section repères sous nouvelle fonctionnalité pour le comportement de mise à jour.


##<a id="june_changes_12"></a>Juin 2012 (version préliminaire)

Les fonctionnalités suivantes a été nouveautées dans la version de novembre du Kit de développement.

* La suppression des entités

    IAsset, IAssetFile, ILocator, IAccessPolicy, maintenant, les objets IContentKey sont supprimés au niveau des objets, c'est-à-dire IObject.Delete(), plutôt que de demander une suppression dans la collection de sites, qui est cloudMediaContext.ObjCollection.Delete(objInstance).

* Repères

    Repères doivent maintenant être créés à l’aide de la méthode CreateLocator et utilisent les valeurs d’énumération LocatorType.SAS ou LocatorType.OnDemandOrigin comme un argument pour le type de locator spécifique que vous voulez créer.

    Nouvelles propriétés ont été ajoutées à repères pour simplifier l’obtenir MU utilisable pour votre contenu. Cette nouvelle conception de repères a été conçue pour fournir davantage de flexibilité pour la future extensibilité tiers et augmenter la facilité d’utilisation pour les applications clientes multimédia.

* Prise en charge de la méthode asynchrone

    Prise en charge asynchrone a été ajouté à toutes les méthodes.


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services Forum MSDN]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services référence de l’API REST]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Pour plus d’informations des Services multimédia]: http://azure.microsoft.com/pricing/details/media-services/
[Entrer des métadonnées]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Métadonnées de sortie]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Distribution de contenu]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[L’indexation des fichiers multimédia avec Indexer de Media Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Utilisation des Services Azure Media Live diffusion en continu]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[À l’aide de chiffrement dynamiques AES 128 et Service de remise clés]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[À l’aide de chiffrement PlayReady dynamique et le Service de remise de licence]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Vue d’ensemble des modèles de licences PlayReady des Services multimédia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Diffusion en continu de stockage du contenu chiffré]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Emballage dynamique]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protection des flux lisse avec PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Réessayer logique dans les Services de support SDK pour .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley annonce la fonction 7 en continu via le Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Contrôle Media Encoder sortie noms du Service]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Créer des superpositions]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Combinaison de séquences vidéo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Mises à jour de Azure Media Services .NET SDK 3.0.0.1 et 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Service de contrôle d’accès Azure Active Directory (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Se connecter aux Services de support avec les Services de support SDK pour .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[outils du sdk d’Azure]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Gestion des Media Services actifs entre plusieurs comptes de stockage]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Gestion des Media Services notifications relatives aux tâches]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
