<properties 
    pageTitle="Vue d’ensemble des modèles de licence Widevine | Microsoft Azure" 
    description="Cette rubrique fournit une vue d’ensemble d’un modèle de licence Widevine qui permet de configurer des licences Widevine." 
    authors="juliako" 
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

#<a name="widevine-license-template-overview"></a>Vue d’ensemble des modèles de licence Widevine

##<a name="overview"></a>Vue d’ensemble

Azure Media Services maintenant vous permet de configurer et demander des licences de Widevine. Lorsque le lecteur de l’utilisateur final essaie de lire le contenu de votre Widevine protégé, une demande est envoyée au service de remise de licence pour obtenir une licence. Si le service de licence approuve la demande, il émet la licence qui est envoyée au client et peut être utilisée pour déchiffrer et lire le contenu spécifié.

Demande de licence Widevine est mis en forme en tant que message JSON.  

Notez que vous pouvez choisir de créer un message vide et aucune valeur seulement « {} » et un modèle de licence est créé avec toutes les valeurs par défaut.  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>Message JSON

Nom | Valeur | Description
---|---|---
charge utile |Chaîne en base 64 codé |La demande de licence envoyée par un client. 
content_id | Chaîne en base 64 codé|Identificateur utilisé pour dérivent KeyId(s) et sur les touches de contenu pour chaque content_key_specs.track_type.
fournisseur |chaîne |Permet de rechercher les stratégies et les touches de contenu. Obligatoire.
nom_stratégie | chaîne |Nom d’une stratégie précédemment enregistrée. Facultatif
allowed_track_types | énumération  | SD_ONLY ou SD_HD. Contrôles qui clés de contenu doivent être inclus dans une licence
content_key_specs | tableau de JSON structures, voir **Contenu clé spécifications** ci-dessous | Une plus grande ampleur contrôle quel contenu touches de direction pour renvoyer. Pour plus d’informations, voir Spécification de clé de contenu en dessous.  Seul d’allowed_track_types et content_key_specs peut être spécifié. 
use_policy_overrides_exclusively | valeur booléenne. Vrai ou faux | Utiliser des attributs de stratégie spécifiés par policy_overrides et ignorer toutes les stratégies enregistrées précédemment.
policy_overrides | JSON structurer, consultez la **Stratégie remplace** ci-dessous | Paramètres de stratégie de cette licence.  En cas de cet actif comporte une stratégie prédéfinie, ces valeurs spécifiées seront utilisées. 
session_init | JSON structurer, consultez **L’initialisation de Session** ci-dessous | Données facultatives passé à la licence.
parse_only | valeur booléenne. Vrai ou faux | La demande de licence est analysée mais aucune licence n’est émis. Toutefois, les valeurs de formulaire à la demande de licence sont retournés dans la réponse.  

##<a name="content-key-specs"></a>Spécifications de clé de contenu 

Si une stratégie préexistante existe, il n’est pas nécessaire de spécifier des valeurs dans la spécification de clé de contenu.  La stratégie préexistante associée à ce contenu permet de déterminer la protection de sortie tels que HDCP et CGMS.  Si une stratégie existante n’est pas enregistrée avec le serveur de licences Widevine, le fournisseur de contenu peut injection les valeurs dans la demande de licence.   


Chaque content_key_specs est obligatoire pour toutes les pistes, quelle que soit l’option use_policy_overrides_exclusively. 


Nom | Valeur | Description
---|---|---
content_key_specs. track_type | chaîne | Nom du type suivi. Si content_key_specs est spécifié dans la demande de licence, veillez à spécifier que tous les types de suivre explicitement. Si vous entraînent Échec de lecture passées 10 secondes. 
content_key_specs  <br/> security_level | UInt32 | Définit les exigences de fiabilité de client pour la lecture. <br/> 1 - basé sur logiciel boîte blanche et au est requis. <br/> 2 - et au logiciel et un filtre masqué est requis. <br/> 3 - les opérations de chiffrement et le matériel de clé doivent être effectuées dans un environnement d’exécution approuvé sauvegardés matériel. <br/> 4 - le chiffrement et décodage de contenu doivent être effectuées dans un environnement d’exécution approuvé sauvegardés matériel.  <br/> 5 - le chiffrement, décodage et tout traitement de l’élément multimédia (compressé et décompressé) doivent être gérées au sein d’un environnement d’exécution approuvé sauvegardés matériel.  
content_key_specs <br/> required_output_protection.HDC | chaîne - parmi : HDCP_NONE, HDCP_V1, HDCP_V2 | Indique si les HDCP est nécessitent
content_key_specs <br/>clé | En base 64 <br/>chaîne encodée|Clé de contenu à utiliser pour cette option. Si spécifié, le track_type ou key_id est requise.  Cette option permet le fournisseur de contenu pour injection de la clé de contenu pour cette piste au lieu de laisser le serveur de licences Widevine générer ou rechercher une clé.
content_key_specs.key_ID| En base 64 codé chaîne binaire, 16 octets | Identificateur unique pour la clé. 


##<a name="policy-overrides"></a>Remplacements de la stratégie 

Nom | Valeur | Description
---|---|---
policy_overrides. can_play | valeur booléenne. Vrai ou faux | Indique que la lecture du contenu est autorisée. Par défaut est false.
policy_overrides. can_persist | valeur booléenne. Vrai ou faux |Indique que la licence peut-être être conservée au stockage non volatiles pour une utilisation hors connexion. Par défaut est false.
policy_overrides. can_renew | valeur booléenne : true ou false |Indique que le renouvellement de cette licence est autorisé. Si vrai, vous pouvez prolonger la durée de la licence par pulsation. Par défaut est false. 
policy_overrides. license_duration_seconds | Int64 | Indique la durée de validité de cette licence spécifique. Une valeur de 0 indique qu’il n’existe aucune limite à la durée. Par défaut est égal à 0. 
policy_overrides. rental_duration_seconds | Int64 | Indique la durée de validité pendant la lecture est autorisée. Une valeur de 0 indique qu’il n’existe aucune limite à la durée. Par défaut est égal à 0. 
policy_overrides. playback_duration_seconds | Int64 | La fenêtre d’affichage de temps une fois que le démarrage de la lecture au sein de la durée de la licence. Une valeur de 0 indique qu’il n’existe aucune limite à la durée. Par défaut est égal à 0. 
policy_overrides. renewal_server_url |chaîne | Toutes les demandes de pulsation (renouvellement) de cette licence sont adressées à l’URL spécifiée. Ce champ est utilisé uniquement si can_renew est vrai.
policy_overrides. renewal_delay_seconds |Int64 |Le nombre de secondes après license_start_time, avant la première tentative de renouvellement. Ce champ est utilisé uniquement si can_renew est vrai. Par défaut est égal à 0 
policy_overrides. renewal_retry_interval_seconds | Int64 | Spécifie le délai en secondes entre les demandes de renouvellement de licence suivantes, en cas d’échec. Ce champ est utilisé uniquement si can_renew est vrai. 
policy_overrides. renewal_recovery_duration_seconds | Int64 | La fenêtre de temps, dans lequel lecture est autorisée à continuer lors de renouvellement est tentative, encore échouer en raison de problèmes de serveur principal avec le serveur de licences. Une valeur de 0 indique qu’il n’existe aucune limite à la durée. Ce champ est utilisé uniquement si can_renew est vrai.
policy_overrides. renew_with_usage | valeur booléenne : true ou false |Indique que la licence est communiquée pour le renouvellement quand l’utilisation est démarrée. Ce champ est utilisé uniquement si can_renew est vrai. 

##<a name="session-initialization"></a>Initialisation de la session

Nom | Valeur | Description
---|---|---
provider_session_token | Chaîne en base 64 codé |Ce jeton de session est passé dans la licence et se trouvent dans renouvellement suivantes.  Le jeton de session n’est pas conservées au-delà de sessions. 
provider_client_token | Chaîne en base 64 codé | Jeton de client pour envoyer la réponse de licence.  Si la demande de licence contient un jeton de client, cette valeur est ignorée. Le jeton du client est conservées au-delà de sessions de licence.
override_provider_client_token | valeur booléenne. Vrai ou faux |Si faux et la demande de licence contient un jeton de client, utilisez le jeton à partir de la demande même si un jeton de client a été spécifié dans cette structure.  Si vrai, toujours utiliser le jeton spécifié dans cette structure.

##<a name="configure-your-widevine-licenses-using-net-types"></a>Configurer vos licences Widevine à l’aide des types .NET

Media Services propose des API .NET qui vous permettent de configurer vos licences Widevine. 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes, telle que définie dans le Kit de développement Media Services .NET

Voici les définitions de ces types.

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>Exemple

L’exemple suivant montre comment utiliser des API .NET pour configurer une licence Widevine simple.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Voir aussi

[L’utilisation du chiffrement courantes PlayReady et/ou Widevine dynamique](media-services-protect-with-drm.md)
