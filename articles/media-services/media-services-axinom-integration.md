<properties 
    pageTitle="À l’aide de Axinom d’effectuer des licences Widevine devant Azure Media Services | Microsoft Azure" 
    description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour faire un flux de données qui est chiffré dynamiquement par AMS avec PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady de Services de support et Widevine licence est fourni par le serveur de licences Axinom." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>À l’aide de Axinom d’effectuer des licences Widevine devant Azure Media Services  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>Vue d’ensemble

Azure Media Services (AMS) a ajouté protection dynamique Google Widevine (voir le [blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) pour plus d’informations). En outre, Azure Media Player (a) a également ajouté Widevine prise en charge (pour plus d’informations, consultez [document a](http://amp.azure.net/libs/amp/latest/docs/) ). Il s’agit d’un succès majeur dans tiret contenu protégé par CENC avec multi-native-DRM (PlayReady et Widevine) en continu sur les navigateurs modernes équipées MSE et EME.

En commençant par Media Services .NET SDK version 3.5.2, Media Services vous permet de configurer le modèle de licence Widevine et d’obtenir des licences Widevine. Vous pouvez également utiliser les partenaires AMS suivants pour vous aider à effectuer des licences Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Cet article décrit comment intégrer et du test du serveur de licences Widevine géré par Axinom. En particulier, il recouvre :  

- Configuration de chiffrement courantes dynamiques avec multi-DRM (PlayReady et Widevine) avec des URL d’acquisition licence correspondante ;
- Générer un jeton JWT afin de répondre aux exigences de serveur de la licence ;
- Développement d’application Azure Media Player qui gère l’acquisition de licences avec l’authentification des jetons JWT ;

L’ensemble du système et le flux de contenu QU'ID clé et les principales, clé valeur de départ, jeton JTW et ses créances peut être mieux décrit par le diagramme suivant.

![TIRET et CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>Protection du contenu

Pour la configuration de protection dynamique et la stratégie de remise clés, voir blog de Mingfei : [comment configurer emballage Widevine avec Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Vous pouvez configurer protection CENC dynamique avec multi-DRM tiret streaming présentant les deux des opérations suivantes :

1. Protection par PlayReady pour MS Edge et IE11, qui peut avoir une restrictions jeton d’autorisation. La stratégie restreint jeton doit être accompagnée d’un jeton émis par un Service (jeton de sécurité), par exemple Azure Active Directory ;
1. Protection Widevine pour Chrome, il peut nécessite l’authentification des jetons avec jeton émis par une autre STS. 

Voir [Génération jeton JWT](media-services-axinom-integration.md#jwt-token-generation) section pour pourquoi Azure Active Directory ne peut pas être utilisé comme un STS Widevine serveur de Axinom de licences.

###<a name="considerations"></a>Considérations relatives à la

1. Vous devez utiliser le Axinom spécifié ID pour générer la clé de contenu pour configurer le service de remise clés de clé clé valeur de départ (8888000000000000000000000000000000000000) et votre généré ou sélectionné. Serveur de licences Axinom émet toutes les licences contenant des clés de contenu en fonction de la même valeur de départ de clé, qui est valide pour le test et de production.
1. L’URL d’acquisition de licence Widevine pour le test : [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP et HTTS sont autorisées.

##<a name="azure-media-player-preparation"></a>Préparation du lecteur média Azure

A 1.4.0 prend en charge la lecture du contenu AMS dynamiquement fourni avec PlayReady et Widevine DRM.
Si le serveur de licences Widevine ne nécessite pas de jeton d’authentification, il est rien supplémentaires que vous devez effectuer pour tester un contenu tiret protégé par Widevine. Pour obtenir un exemple, l’équipe a fournit un simple [exemple](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), dans laquelle vous pouvez voir qu’il fonctionne en périphérie et IE11 avec PlayReady et Chrome avec Widevine.
Le serveur de licences Widevine fourni par Axinom requiert une authentification jeton JWT. Le jeton JWT doit être envoyé avec une demande de licence via un en-tête HTTP « X-AxDRM-Message ». Pour cela, vous devez ajouter le code javascript suivant dans la page web d’hébergement a avant de définir la source :

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Le reste du code a est standard API a obtenus document a [ici](http://amp.azure.net/libs/amp/latest/docs/).

Notez que le code javascript ci-dessus pour l’en-tête d’autorisation personnalisé paramètre est toujours une approche à court terme avant l’agent approche à long terme de gestion du matériel est publié.

##<a name="jwt-token-generation"></a>Génération jeton JWT

Serveur de licences Axinom Widevine pour le test requiert une authentification jeton JWT. En outre, un des demandes dans le jeton JWT est de type d’objet complexes au lieu de type de données primitif.

Malheureusement, Azure AD peut uniquement émettre des jetons JWT types de base. De même, API .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler et JwtPayload) seulement vous permet d’entrer type d’objet complexes en tant que revendications. Toutefois, les revendications sont toujours sérialisées sous forme de chaîne. Par conséquent, nous ne pouvons pas utiliser un des deux pour générer le jeton JWT de demande de licence Widevine.


De John Sheehan [package JWT Nuget](https://www.nuget.org/packages/JWT) répond aux besoins afin que nous allons utiliser ce package Nuget.

Voici le code pour générer jeton JWT avec les revendications nécessaires comme requis par le serveur de licences Axinom Widevine pour le test :

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Serveur de licences Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>Considérations relatives à la

1.  Même si le service de remise de licence PlayReady AMS nécessite « PORTEUR = « précédant un jeton d’authentification, serveur de licences Axinom Widevine ne l’utilise.
2.  La clé de communication Axinom est utilisée comme clé de signature. Notez que la clé est une chaîne hexadécimale, mais il doit être traité comme une série d’octets n’est pas une chaîne lorsque le codage. Pour ce faire, la méthode ConvertHexStringToByteArray.

##<a name="retrieving-key-id"></a>Récupération d’identificateur de clé

Vous avez pouvez remarqué que dans le code pour générer un JWT jeton et les principales ID est requis. Dans la mesure où les besoins jetons JWT prête avant le lecteur chargement a, touche ID doit être récupérées, afin de générer un jeton JWT.

Évidemment, il existe plusieurs méthodes pour obtenir maintenez la touche de clé identifiant. Par exemple, une peut-être stocker les ID de clé ainsi que les métadonnées de contenu dans une base de données. Ou vous pouvez récupérer ID de fichier MPD tiret (Description de la présentation multimédia) de clé. Le code ci-dessous s’applique à ces derniers.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>Résumé

Dernier d’ajout de prise en charge Widevine dans Protection du contenu Azure Media Services et Azure Media Player, nous sommes en mesure d’implémenter en continu de tiret + Multi-native-DRM (PlayReady + Widevine) avec les deux service licence PlayReady dans AMS et Widevine serveur de licences d’Axinom pour les navigateurs modernes suivants :

- Chrome
- Microsoft Edge sur Windows 10
- Internet Explorer 11 sur Windows 8.1 et Windows 10
- Firefox (bureau) et Safari sur Mac (pas iOS) sont également acceptés via Silverlight et la même URL avec Azure Media Player

Les paramètres suivants sont requis dans le serveur de licences solution mini tirant parti Axinom Widevine. À l’exception de clé ID, le reste des paramètres sont fournis par Axinom en fonction de leur configuration serveur Widevine.


Paramètre|Comment elle est utilisée
---|---
ID de clé de communication|Doit être inclus en tant que valeur de la demande « com_key_id » dans jeton JWT (voir [cette](media-services-axinom-integration.md#jwt-token-generation) section).
Clé de communication|Doit être utilisé comme la clé de signature de jeton JWT (voir [cette](media-services-axinom-integration.md#jwt-token-generation) section).
Valeur de départ clés|Doit être utilisé pour générer la clé de contenu dont le contenu donné ID de clé (voir [cette](media-services-axinom-integration.md#content-protection) section).
URL d’acquisition de licence Widevine|Doit être utilisé dans la configuration de la stratégie de remise de biens pour le tiret streaming (voir [cette](media-services-axinom-integration.md#content-protection) section).
ID de clé de contenu|Doit être inclus dans le cadre de la valeur de réclamer Message droit du jeton JWT (voir [cette](media-services-axinom-integration.md#jwt-token-generation) section). 


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Accusés de réception 

Nous aimerions confirmer les suivi des personnes qui contribué à la création de ce document : Kristjan Jõgi de Axinom Mingfei Yan et Amit Rajput.
