<properties 
    pageTitle="Vue d’ensemble du contenu de protection | Microsoft Azure" 
    description="Cet article offre une vue d’ensemble de la protection du contenu avec Media Services." 
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
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="juliako"/>

#<a name="protecting-content-overview"></a>Vue d’ensemble du contenu de protection


Microsoft Azure Media Services vous permet de sécuriser votre support entre le moment où qu'il quitte votre ordinateur via stockage, traitement et la livraison. Media Services vous permet d’effectuer votre contenu à la demande et en temps réel chiffré dynamiquement avec chiffrement AES (Advanced Standard) (à l’aide de clés de chiffrement 128 bits) ou l’un de le DRMs principales : Microsoft PlayReady, Google Widevine et FairPlay d’Apple. Media Services fournit également un service permettant de clés AES et DRM (PlayReady, Widevine et FairPlay) des licences aux clients autorisés. 

L’image suivante montre les flux de travail de protection du contenu qui prend en charge AMS. 

![Protéger avec PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Pour pouvoir utiliser le chiffrement dynamique, vous devez d’abord obtenir au moins une unité réservée diffusion en continu sur le point de terminaison de diffusion en continu à partir de laquelle vous souhaitez diffuser le contenu chiffré.

Cette rubrique explique les [concepts et la terminologie](media-services-content-protection-overview.md) pertinentes pour comprendre la protection du contenu avec AMS. La rubrique contient également [des liens](media-services-content-protection-overview.md#common-scenarios) vers des rubriques qui montrent comment effectuer des tâches de protection du contenu. 

##<a name="dynamic-encryption"></a>Chiffrement dynamiques

Microsoft Azure Media Services vous permet d’effectuer votre contenu chiffré dynamiquement avec DRM chiffrement AES clé d’effacement ou : Microsoft PlayReady, Google Widevine et FairPlay d’Apple.

Pour l’instant, vous pouvez chiffrer les formats de diffusion en continu suivants : TLS, MPEG tiret et Smooth Streaming. Vous ne pouvez pas chiffrer HDS streaming format ou progressive télécharge.

Si vous souhaitez que pour les Services de support chiffrer un bien, vous devez associer une clé de chiffrement (CommonEncryption ou EnvelopeEncryption) à vos ressources et également configurer les stratégies d’autorisation pour la clé.

Vous devez également configurer la stratégie de remise de l’actif. Si vous voulez diffuser la gestion du stockage chiffré, veillez à spécifier la manière dont vous souhaitez effectuer en configurant la stratégie de remise de biens.

Lorsqu’un flux de données est demandé par un lecteur, Media Services utilise la clé spécifiée pour dynamiquement chiffrer votre contenu à l’aide de la clé d’effacement AES ou chiffrement DRM. Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de remise clés. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.

>[AZURE.NOTE]Pour tirer parti de chiffrement dynamiques, vous devez d’abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu chiffré. Pour plus d’informations, voir [comment échelle Media Services](media-services-portal-manage-streaming-endpoints.md).

##<a name="storage-encryption"></a>Chiffrement de stockage

Utilisation du chiffrement stockage pour chiffrer votre contenu effacer localement en utilisant le chiffrement AES 256 bits et puis téléchargez-le sur le stockage Azure où il est stocké chiffrées au reste. Ressources protégés par le chiffrement de stockage sont automatiquement non chiffrés et placés dans un système de fichiers chiffrés avant le codage et éventuellement chiffrés avant de le télécharger comme un nouvel actif de sortie. Le cas d’utilisation principal pour le chiffrement de stockage est lorsque vous voulez protéger vos fichiers de média d’entrée de haute qualité avec chiffrement fort inactives sur disque.

Afin de fournir une gestion du stockage chiffré, vous devez configurer stratégie de remise de biens afin que Media Services sache comment vous voulez présenter votre contenu. Avant de vos biens peuvent être diffusés en continu, le serveur de diffusion supprime le chiffrement de stockage et flux de données votre contenu à l’aide de la stratégie de livraison spécifiée (par exemple, AES, chiffrement courantes ou aucun chiffrement).

## <a name="common-encryption-cenc"></a>Chiffrement courantes (CENC)

Chiffrement courantes est utilisé pour chiffrer votre contenu avec PlayReady ou / et Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Le chiffrement des CBC aapl

Liste des CBC aapl est utilisé pour chiffrer votre contenu avec FairPlay.

## <a name="envelope-encryption"></a>Chiffrement de l’enveloppe 

Utilisez cette option si vous voulez protéger votre contenu avec la touche effacer AES 128. Si vous voulez une option la plus sûre, choisissez les DRMs répertoriés dans cette rubrique. 

##<a name="licenses-and-keys-delivery-service"></a>Service de remise des licences et clés

Media Services fournit un service permettant de licences DRM (PlayReady, Widevine, FairPlay) et AES effacer les clés aux clients autorisés. Vous pouvez utiliser [le portail Azure](media-services-portal-protect-content.md), API REST ou Media Services SDK pour .NET pour configurer des stratégies d’autorisation et d’authentification pour les licences et les touches.

##<a name="token-restriction"></a>Restriction jeton

La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou restriction des jetons. La stratégie restreint jeton doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans JSON Web jeton (JWT) et le format Simple Web jetons SWT (). Media Services ne fournit pas de Services de jetons banque d’informations sécurisé. Vous pouvez créer un STS personnalisé ou tirer parti de Microsoft Azure ACS aux jetons de problème. Le STS doit être configuré pour créer un jeton connecté avec les revendications spécifiées clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Le service de remise clés Media Services renvoie la clé demandée (ou la licence) pour le client si le jeton est valide et les revendications dans le jeton adapté ceux configurés la clé (ou la licence).

Lors de la configuration du jeton restreint stratégie, vous devez spécifier la clé primaire de vérification, les émetteur et les paramètres de l’audience. La clé primaire vérification contient la clé de signature avec le jeton, émetteur est le service de jeton sécurisé qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource le jeton autorise l’accès à. Le service de remise clés Media Services valide que les valeurs suivantes dans le jeton correspondent aux valeurs dans le modèle.

##<a name="streaming-urls"></a>URL de diffusion en continu

Si votre bien a été chiffré avec plusieurs DRM, vous devez utiliser une balise de chiffrement dans l’URL de diffusion en continu : (format = 'm3u8-aapl', le chiffrement = « xxx »).

Les considérations suivantes s’appliquent :

- Uniquement zéro ou un type de chiffrement peut être spécifié.
- Type de chiffrement ne doit être indiquée dans l’url si seule une chiffrement a été appliqué à la ressource.
- Type de chiffrement respecte la casse.
- Vous pouvez spécifier les types de chiffrement suivants :  
    - **cenc**: chiffrement courantes (Playready ou Widevine)
    - **liste des CBC aapl**: Fairplay
    - **CBC**: le chiffrement AES enveloppe.

##<a name="common-scenarios"></a>Scénarios courants

Les rubriques suivantes montrent comment protéger le contenu dans le stockage, remettre multimédia dynamiquement chiffré, utiliser le service de remise AMS clé/licence

- [Protéger avec AES](media-services-protect-with-aes128.md) 
- [Protéger avec PlayReady et/ou Widevine](media-services-protect-with-drm.md)
- [Diffuser votre protégé contenu TLS avec Apple FairPlay et/ou PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Scénarios supplémentaires

- [Comment intégrer le service de licences PlayReady Azure avec votre propre serveur chiffreur/la diffusion](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [À l’aide de castLabs d’effectuer des licences DRM devant Azure Media Services](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Liens connexes

[Annonce PlayReady en tant que service et chiffrement AES, dynamique avec Azure Media Services](http://mingfeiy.com/playready)

[Tarifs remise licence Azure Media Services PlayReady expliqués](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Comment déboguer pour flux chiffré AES dans Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Authenitcation jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Intégrer Azure Media Services OWIN MVC en fonction de l’application avec Azure Active Directory et de limiter la remise de clés contenue basée sur les revendications JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Utiliser Azure ACS aux jetons de problème](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
