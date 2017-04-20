<properties 
    pageTitle="Configurer la stratégie de l’autorisation clé de contenu à l’aide du portail Azure | Microsoft Azure" 
    description="Découvrez comment configurer une stratégie d’autorisation pour une clé de contenu." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>



#<a name="configure-content-key-authorization-policy"></a>Configurer la stratégie d’autorisation clé contenu
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services vous permet d’effectuer des flux MPEG-tiret, Smooth Streaming et diffusion Live HTTP (TLS) protégés par le chiffrement AES (Advanced Standard) (à l’aide de clés de chiffrement 128 bits) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS vous permet également d’effectuer des flux tiret chiffrées avec Widevine DRM. PlayReady et Widevine sont chiffrés par la spécification de chiffrement courantes (ISO/IEC 23001-7 CENC).

Media Services fournit également un **Service de remise de clé/licence** à partir de laquelle les clients peuvent-ils clés AES ou PlayReady/Widevine des licences pour lire le contenu chiffré.

Cette rubrique indique comment utiliser le portail Azure pour configurer la stratégie d’autorisation clé contenu. La clé ultérieurement peut être utilisée pour chiffrer dynamiquement votre contenu. Formats de note actuellement peut chiffrer la diffusion en continu suivants : TLS, MPEG tiret et Smooth Streaming. Vous ne pouvez pas chiffrer HDS streaming format ou progressive télécharge.

Lorsqu’un lecteur demande un flux de données est défini sur dynamiquement chiffré, Media Services utilise la clé configurée pour chiffrer dynamiquement votre contenu en utilisant le chiffrement AES ou DRM. Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de remise clés. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.


Si vous envisagez de disposer de plusieurs touches contenus ou pour spécifier une URL du **Service de remise de clé/licence** autre que le service de remise clés Media Services, utilisez Media Services .NET SDK ou des API REST.

[Configurer la stratégie de l’autorisation clé de contenu à l’aide de Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurer la stratégie de l’autorisation clé de contenu à l’aide de l’API REST de Services de support](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>Certaines considérations s’appliquent :

- Pour pouvoir utiliser emballage dynamique et le chiffrement dynamique, vous devez vous assurer d’avoir au moins une unité réservée la diffusion. Pour plus d’informations, voir [comment mettre à l’échelle d’un Service de support](media-services-portal-manage-streaming-endpoints.md).
- Vos biens doit contenir un ensemble de débit adaptive MP4s ou débit adaptive Smooth Streaming fichiers. Pour plus d’informations, voir [Encoder un bien](media-services-encode-asset.md).
- Le service de remise de clé met en cache ContentKeyAuthorizationPolicy et ses objets associés (options de stratégie et restrictions) pendant 15 minutes.  Si vous créez un ContentKeyAuthorizationPolicy et que vous spécifiez pour utiliser une restriction « Jeton », puis tester et puis mettez à jour la stratégie à la restriction « Ouvrir », vous devrez patienter environ 15 minutes avant que la stratégie bascule vers la version « Ouverte » de la stratégie.


##<a name="how-to-configure-the-key-authorization-policy"></a>Comment : configurer la stratégie d’autorisation clés

Pour configurer la stratégie d’autorisation clés, sélectionnez la page de **PROTECTION du contenu** .

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation clé contenu peut avoir **Ouvrir**, **jeton**ou les restrictions d’autorisation **IP** (**IP** peut être configuré avec reste ou .NET SDK).

###<a name="open-restriction"></a>Restriction Ouvrir

Les restrictions **d’Ouvrir** signifie que le système doit apporter la clé à une autre personne qui la crée une demande de clé. Cette restriction peut être utile pour des fins de test.

![OpenPolicy][open_policy]

###<a name="token-restriction"></a>Restriction jeton

Pour choisir la stratégie restreint jeton, appuyez sur le bouton **jeton** .

La stratégie **jeton** restreint doit être accompagnée d’un jeton émis par un **Service de jeton de banque d’informations sécurisé** (STS). Media Services prend en charge les jetons dans les formats de **Jetons Web Simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) et **JSON Web jeton** (JWT). Pour plus d’informations, voir [l’authentification des jetons JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services n’offre pas de **Services de jetons banque d’informations sécurisé**. Vous pouvez créer un STS personnalisé ou tirer parti de Microsoft Azure ACS aux jetons de problème. Le STS doit être configuré pour créer un jeton connecté avec les revendications spécifiées clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Le service de remise clés Media Services renvoie la clé de chiffrement au client si le jeton est valide et les revendications dans le jeton correspondent à ceux qui sont configurés pour la clé de contenu. Pour plus d’informations, voir [Utiliser Azure ACS aux jetons de problème](http://mingfeiy.com/acs-with-key-services).

Lors de la configuration du **jeton** restreint stratégie, vous devez définir les valeurs de **clé de vérification**, **émetteur** et **audience**. La clé primaire vérification contient la clé de signature avec le jeton, émetteur est le service de jeton sécurisé qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource le jeton autorise l’accès à. Le service de remise clés Media Services valide que les valeurs suivantes dans le jeton correspondent aux valeurs dans le modèle.

###<a name="playready"></a>PlayReady

Lorsque vous protégez votre contenu avec **PlayReady**, un des choses que vous devez spécifier dans votre stratégie d’autorisation est une chaîne XML qui définit le modèle de licence PlayReady. Par défaut, la stratégie suivante est définie :

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

Vous pouvez cliquer sur le bouton **Importer des données xml de stratégie** et fournir une autre XML conformes au schéma XML défini [ici](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

