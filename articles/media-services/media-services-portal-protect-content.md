<properties 
    pageTitle="Configuration des stratégies de protection du contenu à l’aide du portail Azure | Microsoft Azure" 
    description="Cet article explique comment utiliser le portail Azure pour configurer des stratégies de protection du contenu. Également l’article vous explique comment activer le chiffrement dynamique pour vos ressources." 
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
    ms.date="10/24/2016"    
    ms.author="juliako"/>

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configuration des stratégies de protection du contenu à l’aide du portail Azure

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services (AMS) vous permet de sécuriser votre support entre le moment où qu'il quitte votre ordinateur via stockage, traitement et la livraison. Media Services vous permet d’effectuer votre contenu chiffré dynamiquement avec chiffrement AES (Advanced Standard) (à l’aide de clés de chiffrement 128 bits), chiffrement courantes (CENC) à l’aide de PlayReady et/ou Widevine DRM et Apple FairPlay. 

AMS fournit un service permettant de licences DRM et AES effacer les clés aux clients autorisés. Le portail Azure vous permet de créer une **stratégie d’autorisation clé/licence** pour tous les types de chiffrement.

Cet article explique comment configurer des stratégies de protection du contenu avec le portail Azure. Également l’article vous explique comment appliquer chiffrement dynamiques à vos ressources.

> [AZURE.NOTE]  Si vous avez utilisé le portail classique Azure pour créer des stratégies de protection, les stratégies ne peuvent pas s’afficher dans le [portail Azure](https://portal.azure.com/). Toutefois, toutes les stratégies de l’ancien existent toujours. Vous pouvez examiner les Azure Media Services .NET SDK ou l’outil [Azure Media-Services l’Explorateur](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (pour afficher les stratégies, avec le bouton droit sur la ressource -> Affichage -> informations (F4), cliquez sur l’onglet contenu clés -> cliquez sur la touche). 
> 
> Si vous voulez chiffrer vos biens à l’aide de nouvelles stratégies, les configurer avec le portail Azure, cliquez sur Enregistrer et réappliquer le chiffrement dynamique. 

## <a name="start-configuring-content-protection"></a>Démarrer la configuration de la protection de contenu

Pour utiliser le portail pour démarrer la configuration de la protection du contenu, globale à votre compte AMS, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Sélectionnez **paramètres** > **protection du contenu**.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>Stratégie d’autorisation clé/licence

AMS prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clé ou la licence. La stratégie d’autorisation clé contenu doit être configurée par vous et remplie par votre client dans l’ordre de la clé/licence pour être delived au client. La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : **Ouvrir** ou **jeton** restriction.

Le portail Azure vous permet de créer une **stratégie d’autorisation clé/licence** pour tous les types de chiffrement.

###<a name="open"></a>Ouvrir 

Ouvre restriction signifie que le système doit apporter la clé à une autre personne qui la crée une demande de clé. Cette restriction peut être utile à des fins de test. 

### <a name="token"></a>Jetons

La stratégie restreint jeton doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans JSON Web jeton (JWT) et le format Simple Web jetons SWT (). Media Services ne fournit pas de Services de jetons banque d’informations sécurisé. Vous pouvez créer un STS personnalisé ou tirer parti de Microsoft Azure ACS aux jetons de problème. Le STS doit être configuré pour créer un jeton connecté avec les revendications spécifiées clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Le service de remise clés Media Services renvoie la clé demandée (ou la licence) pour le client si le jeton est valide et les revendications dans le jeton adapté ceux configurés la clé (ou la licence).

Lors de la configuration du jeton restreint stratégie, vous devez spécifier la clé primaire vérification, émetteur et les paramètres de l’audience. La clé primaire vérification contient la clé de signature avec le jeton, émetteur est le service de jeton sécurisé qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource le jeton autorise l’accès à. Le service de remise clés Media Services valide que les valeurs suivantes dans le jeton correspondent aux valeurs dans le modèle.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Modèle de droits PlayReady

Pour plus d’informations sur le modèle de droits PlayReady, voir [Vue d’ensemble du modèle de licence PlayReady Media Services](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Non permanente

Si vous configurez licence comme non permanent, il est conservé uniquement en mémoire pendant que le lecteur est à l’aide de la licence.  

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Permanente

Si vous configurez la licence comme permanente, il est enregistré dans un stockage permanent sur le client.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Modèle de droits Widevine

Pour plus d’informations sur le modèle de droits Widevine, voir [Vue d’ensemble des modèles de licence Widevine](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Base

Lorsque vous sélectionnez **simple**, le modèle sera créé avec toutes les valeurs par défaut.

### <a name="advanced"></a>Avancées

Pour une explication détaillée sur option avance des configurations Widevine, consultez [la](media-services-widevine-license-template-overview.md) rubrique suivante.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuration de FairPlay

Pour activer le chiffrement FairPlay, vous devez fournir le certificat de l’application et Application Secret clé (ASK) via l’option de Configuration FairPlay. Pour plus d’informations sur la configuration de FairPlay et configuration requise, voir [cet](media-services-protect-hls-with-fairplay.md) article.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Appliquer le chiffrement dynamique à vos ressources

Pour tirer parti de chiffrement dynamiques, vous devez effectuer les opérations suivantes :

- Encoder votre fichier source dans un jeu de fichiers MP4 adaptive débit.
- Obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir de laquelle vous souhaitez présenter votre contenu. Pour plus d’informations, voir [comment mettre à l’échelle de diffusion en continu unités réservées à la demande](media-services-portal-manage-streaming-endpoints.md).

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Sélectionner un bien que vous voulez chiffrer

Pour afficher tous vos actifs, sélectionnez **paramètres** > **actifs**.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Chiffrer avec AES ou DRM

Une fois que vous appuyez sur **chiffrer** sur un bien, vous sont présentées avec deux options : **AES** ou **DRM**. 

#### <a name="aes"></a>AES

Effacer le chiffrement clé est activé sur tous les protocoles de diffusion en continu AES : Smooth Streaming TLS et MPEG-tiret.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

Lorsque vous sélectionnez l’onglet DRM, vous sont présentées avec des stratégies de protection du contenu de différentes manières (que vous devez configurer à ce stade) + un ensemble de diffusion en continu de protocoles.

- **PlayReady et Widevine par un tiret demi-cadratin MPEG** - chiffre dynamiquement votre flux MPEG-tiret avec PlayReady et Widevine DRMs.
- **PlayReady et Widevine avec MPEG-tiret + FairPlay avec TLS** - dynamiquement chiffre vous flux MPEG-tiret avec PlayReady et Widevine DRMs. Chiffre également votre flux TLS avec FairPlay.
- **PlayReady uniquement avec Smooth Streaming, TLS et MPEG-tiret** - dynamiquement chiffre Smooth Streaming, TLS, flux MPEG-tiret avec PlayReady DRM.
- **Widevine uniquement avec MPEG-tiret** - dynamiquement chiffre vous MPEG-tiret avec Widevine DRM.
- **FairPlay uniquement avec TLS** - chiffre dynamiquement votre flux TLS avec FairPlay.

Pour activer le chiffrement FairPlay, vous devez fournir le certificat de l’application et Application Secret clé (ASK) via l’option de Configuration FairPlay de la cuillère de paramètres de Protection du contenu.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Une fois que vous effectuez la sélection de chiffrement, appuyez sur **Appliquer**.

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





