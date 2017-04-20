<properties 
    pageTitle="À l’aide de castLabs d’effectuer des licences Widevine devant Azure Media Services | Microsoft Azure" 
    description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour faire un flux de données qui est chiffré dynamiquement par AMS avec PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady de Services de support et Widevine licence est fourni par le serveur de licences castLabs." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>À l’aide de castLabs d’effectuer des licences Widevine devant Azure Media Services

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>Vue d’ensemble

Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour faire un flux de données qui est chiffré dynamiquement par AMS avec PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady de Services de support et Widevine licence est fourni par le serveur de licences **castLabs** .

Pour la lecture en continu du contenu protégé par CENC (PlayReady et/ou Widevine), vous pouvez utiliser [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Pour plus d’informations, voir [document a](http://amp.azure.net/libs/amp/latest/docs/) .

Le diagramme suivant illustre un haut niveau Azure Media Services et l’architecture d’intégration castLabs.

![intégration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>Configuration système par défaut

- Contenu multimédia est stocké dans AMS.
- ID de clé de clés de contenus sont stockés dans castLabs et AMS.
- castLabs et AMS ont jeton d’authentification intégrée. Les sections suivantes décrivent les jetons d’authentification. 
- Lorsque les demandes des clients au flux de la vidéo, le contenu est chiffré dynamiquement avec **Chiffrement courantes** (CENC) et de dynamiquement AMS Smooth Streaming et tiret. Nous fournissons également chiffrement de flux élémentaire PlayReady M2TS pour le protocole de diffusion en continu TLS.
- Licences PlayReady est extraite de serveur de licences AMS et Widevine licence est extraite de serveur de licences castLabs. 
- Media Player décide automatiquement les licences pour récupérer en fonction de la fonctionnalité de plateforme client. 

##<a name="authentication-token-generation-for-getting-a-license"></a>Génération jeton d’authentification pour obtenir une licence

CastLabs et AMS prend en charge le format du jeton JWT (JSON Web jeton) permettant d’autoriser une licence. 

###<a name="jwt-token-in-ams"></a>Jeton JWT dans AMS 

Le tableau suivant décrit jeton JWT dans AMS. 

Émetteur|Chaîne de l’émetteur à partir de la choisie Service (jeton de sécurité)
---|---
Audience|Chaîne audience à partir de l’enquête STS utilisé
Revendications|Un ensemble de revendications
NotBefore|Démarrer la validité du jeton
Arrive à expiration|Fin validité du jeton
Valeur|La clé est partagée par le serveur de licences PlayReady, castLabs serveur de licences et SharePoint Team Services, il peut s’agir symétrique ou asymétriques clé.

###<a name="jwt-token-in-castlabs"></a>Jeton JWT dans castLabs

Le tableau suivant décrit jeton JWT dans castLabs. 

Nom|Description
---|---
optData|Une chaîne JSON contenant des informations vous concernant. 
CRT|Une chaîne JSON contenant des informations sur la ressource, ses droits de lecture et informations licence.
IAT|Date et heure actuel dans la version.
JTI|Un identificateur unique sur ce jeton (chaque jeton peut uniquement être utilisé une fois dans le système castLabs).

##<a name="sample-solution-set-up"></a>Configurer la solution de l’échantillon 

La [solution de l’échantillon](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) comprend deux projets :

-   Application console pouvant être utilisées pour définir des restrictions de DRM sur un bien déjà ingéré, pour PlayReady et Widevine.
-   Une Application Web qui attribue des jetons, qui peuvent être considérées comme une version très simplifié d’un STS.


Pour utiliser l’application console :

1.  Modifier le fichier app.config pour configurer les informations d’identification AMS, informations d’identification castLabs, STS configuration et la clé partagée.
2.  Télécharger un bien dans AMS.
3.  Obtenir de l’UUID du bien téléchargé et modifier 32 de ligne dans le fichier Program.cs :

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  Utiliser un réfbien pour nommer la ressource dans le système castLabs (44 ligne dans le fichier Program.cs).

    Vous devez définir réfbien pour **castLabs**; Il doit être une chaîne alphanumérique unique.

5.  Exécutez le programme.


Pour utiliser l’Application Web (STS) :

1.  Modifier le fichier web.config à marchand de castlabs le programme d’installation ID, la configuration de SharePoint Team Services et la clé partagée.
2.  Déployer à des sites Web Azure.
3.  Accédez au site Web.

##<a name="playing-back-a-video"></a>Lecture d’une vidéo

Pour lire une vidéo chiffrée avec chiffrement courantes (PlayReady et/ou Widevine), vous pouvez utiliser le [Lecteur multimédia Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Lorsque vous exécutez l’application console, l’ID de clé de contenu et l’URL du manifeste sont répercutées.

1.  Ouvrir un nouvel onglet et lancer votre STS : http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.  Accédez à [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.  Collez l’URL de diffusion en continu.
4.  Cliquez sur la case à cocher **Options avancées** .
5.  Dans la liste déroulante **Protection** , sélectionnez PlayReady et/ou Widevine.
6.  Coller le jeton que vous avez obtenu à partir de votre STS dans la zone de texte jeton. 
    
    Le serveur de licences castLab n’a pas besoin du « porteur = « préfixe devant le jeton. Donc supprimez qui avant d’envoyer le jeton.
7.  Mettre à jour le lecteur.
8.  La vidéo doit être en cours de lecture.


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
