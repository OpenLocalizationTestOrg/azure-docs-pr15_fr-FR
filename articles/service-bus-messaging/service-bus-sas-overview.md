<properties
    pageTitle="Partagé vue d’ensemble des Signatures Access | Microsoft Azure"
    description="Quelles sont les Signatures de Access partagés, comment fonctionnent-ils et comment les utiliser à partir de nœud, PHP et c#."
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>Accès partagé Signatures

*Partagé des Signatures Access* (Sa) constituent le mécanisme principal de sécurité Bus des services, y compris les événements Hubs, demandé de messagerie (files d’attente et rubriques) et relais de messagerie. Cet article traite partagés accès Signatures, leur fonctionnement et comment les utiliser d’une manière indépendant de la plateforme.

## <a name="overview-of-sas"></a>Vue d’ensemble des associations de sécurité

Les Signatures accès partagé sont un mécanisme d’authentification basé sur hachage sécurisé ça-256 ou URI. Associations de sécurité sont un mécanisme extrêmement puissant qui est utilisé par tous les services Bus des services. En cours d’utilisation réelle, sa présente deux composants : une *Stratégie d’accès partagés* et une *Signature de Access partagé* (souvent appelée un *jeton*).

Vous trouverez plus d’informations sur les Signatures d’accès partagé à Service dans [l’authentification partagé Access Signature à Service](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Stratégie d’accès partagé

En savoir sur les associations de sécurité est important que tout commence par une stratégie. Pour chaque stratégie, vous décidez sur trois éléments d’information : **nom**, **étendue**et **autorisations**. C’est le **nom de** ce que ; un nom unique dans cette étendue. L’étendue est assez simple : il s’agit de l’URI de la ressource en question. Pour un espace de noms Bus des services, l’étendue est le nom de domaine complet (FQDN), tel que `https://<yournamespace>.servicebus.windows.net/`.

Les autorisations disponibles pour une stratégie sont relativement explicites :

  + Envoyer
  + Écouter
  + Gérer

Après avoir créé la stratégie, il reçoit une *Clé primaire* et une *Clé secondaire*. Il s’agit des touches et forts. Ne pas perdre les ou perdre leur - elle pourra toujours disponibles dans le [portail Azure][]. Vous pouvez utiliser une des clés générées, et vous pouvez les régénérer à tout moment. Toutefois, si vous régénérez ou modifiez la clé primaire dans la stratégie, les Signatures accès partagé créé à partir de celui-ci sont annulées.

Lorsque vous créez un espace de noms Bus des services, une stratégie est automatiquement créée pour l’espace de noms entier appelé **RootManageSharedAccessKey**, et cette stratégie a toutes les autorisations. Vous ne vous connecter en tant que **racine**, afin de ne pas utiliser cette stratégie sauf s’il existe une bonne raison. Vous pouvez créer des stratégies supplémentaires dans l’onglet **configurer** l’espace de noms dans le portail. Il est important de note qu’un seul niveau dans Service marché de l’arborescence (espace de noms, file d’attente, Hub de l’événement, etc.) ne peut avoir jusqu'à 12 stratégies attachées.

## <a name="shared-access-signature-token"></a>Signature accès partagé (jeton)

La stratégie elle-même n’est pas le jeton d’accès Bus des services. Il est l’objet à partir de laquelle le jeton d’accès est généré - en utilisant soit la clé primaire ou secondaire. Le jeton est généré en créant avec soin une chaîne au format suivant :

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Où `signature-string` s’agit du hachage ça 256 de l’étendue du jeton (**étendue** comme décrit dans la section précédente) avec un CRLF ajouté et un délai d’expiration (en secondes depuis l’origine : `00:00:00 UTC` 1er janvier 1970).

Le hachage est semblable au suivant pseudo-code et renvoie 32 octets.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Les valeurs non hachées sont dans la chaîne **SharedAccessSignature** afin que le destinataire peut calculer le hachage avec les mêmes paramètres, pour vous assurer qu’elle renvoie le même résultat. L’URI Spécifie l’étendue, et le nom de clé identifie la stratégie à utiliser pour calculer le hachage. Ceci est important d’un point de vue de sécurité. Si la signature ne correspond pas à celui qui calcule le destinataire (Service Bus), l’accès est refusé. À ce stade, vous pouvez être assurer que l’expéditeur ait accès à la clé et doit être accordée les droits spécifiés dans la stratégie.

## <a name="generating-a-signature-from-a-policy"></a>Génération d’une signature à partir d’une stratégie

Comment réellement faire cela dans le code ? Examinons quelques d'entre eux.

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35 ;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>À l’aide de la Signature accès partagé (au niveau HTTP)
 
À présent que vous savez comment créer des Signatures accès partagé n’importe quelle entité dans Service marché, vous êtes prêt à effectuer une publication HTTP :

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
N’oubliez pas, cela fonctionne pour tous les éléments. Vous pouvez créer des associations de sécurité pour une file d’attente, sujet, abonnement, concentrateur événement ou relais. Si vous utilisez identité par publisher pour événement Hubs, vous simplement ajoutez `/publishers/< publisherid>`.

Si vous donnez un expéditeur ou un client un jeton associations de sécurité, elle n’a pas la clé directement, et qu’ils ne peuvent pas inverser le hachage pour les obtenir. Dès lors, vous avez contrôle qu’ils peuvent accéder et sur la façon de temps. Un important à retenir est que si vous modifiez la clé primaire de la stratégie, les Signatures accès partagé créé à partir de celui-ci seront incorrectes.

## <a name="using-the-shared-access-signature-at-amqp-level"></a>À l’aide de la Signature accès partagé (au niveau AMQP)

Dans la section précédente, vous avez appris à utiliser le jeton associations de sécurité avec une demande HTTP POST pour envoyer des données au Service Bus. Comme vous le savez, vous pouvez accéder à l’aide de l’avancées Message Queuing Protocol (AMQP) qui est le protocole préféré à utiliser pour des raisons de performances, dans de nombreux scénarios Bus des services. L’utilisation de jetons associations de sécurité avec AMQP est décrite dans le document [AMQP Claim-Based sécurité Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) qui se trouve dans un brouillon de travail aujourd'hui depuis 2013 mais bien pris en charge par Azure.

Avant de commencer à envoyer des données à Bus des services, l’éditeur doit envoyer le jeton associations de sécurité au sein d’un message AMQP à un nœud AMQP bien défini nommé **$cbs** (vous pouvez le voir file d’attente « spécial » utilisées par le service pour acquérir et valider tous les jetons associations de sécurité). L’éditeur doit spécifier le champ **ReplyTo** à l’intérieur du message AMQP ; Il s’agit le nœud dans lequel le service a répondu à l’éditeur avec le résultat de la validation des jeton (un modèle de demande/réponse simple entre publisher et service). Création de ce nœud de réponse « à la volée, « parler « création dynamique de nœud distant » comme décrit à la spécification AMQP 1.0. Après avoir vérifié que le jeton associations de sécurité est valide, l’éditeur peut aller vers l’avant et commencer à envoyer des données au service.

Les étapes suivantes montrent comment envoyer le jeton associations de sécurité avec protocole AMQP à l’aide de la bibliothèque [AMQP.Net légère](https://github.com/Azure/amqpnetlite) . Cette option est utile si vous ne pouvez pas utiliser le Kit de développement Bus officiel du Service (par exemple sur WinRT, .net Compact Framework, .net Framework Micro et Mono) développement dans C\#. Bien entendu, cette bibliothèque est utile pour aider à comprendre la sécurité basée sur les revendications fonctionne au niveau de la AMQP, comme vous l’avez vu son fonctionnement au niveau HTTP (avec une demande HTTP POST et le jeton de sa envoyé à l’intérieur de l’en-tête « Autorisation »). Si vous n’avez pas besoin ces connaissances approfondies sur AMQP, vous pouvez utiliser le Kit de développement de Bus Service officiel avec .net applications Framework, ce qui fera pour vous.

### <a name="c35"></a>C & #35 ;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

La `PutCbsToken()` méthode reçoit la *connexion* (instance de classe AMQP connexion en tant que fournies par la [bibliothèque AMQP .NET légère](https://github.com/Azure/amqpnetlite)) qui représente la connexion TCP au service et le paramètre de *sasToken* jeton associations de sécurité à envoyer. 

> [AZURE.NOTE] Il est important que la connexion est créée avec **mécanisme d’authentification SASL définie sur externe** (et non le brut par défaut avec nom d’utilisateur et mot de passe utilisés lorsque vous n’avez pas besoin d’envoyer le jeton associations de sécurité).

Ensuite, l’éditeur crée deux liens AMQP pour envoyer le jeton associations de sécurité et recevoir la réponse (le résultat de la validation des jetons) à partir du service.

Le message AMQP contient un ensemble de propriétés et plus d’informations qu’un simple message. Le jeton associations de sécurité est le corps du message (à l’aide de son constructeur). La propriété **« ReplyTo »** est définie sur le nom du nœud pour recevoir le résultat de la validation sur le lien récepteur (vous pouvez modifier son nom si vous souhaitez, et il sera créé dynamiquement par le service). Les propriétés personnalisées/application trois derniers sont utilisées par le service pour indiquer de quel type d’opération doit s’exécuter. Comme décrit à la spécification de brouillon CBS, ils doivent être le **nom de l’opération** (« place-jeton »), le **type de jeton** (dans ce cas, un « servicebus.windows.net:sastoken ») et le **« nom » de l’audience** à laquelle le jeton s’applique (l’entité entière).

Après avoir envoyé le jeton associations de sécurité sur le lien de l’expéditeur, l’éditeur doit lire la réponse sur le lien récepteur. La réponse est un simple message AMQP avec une propriété d’application nommé **« code d’état »** qui peut contenir les mêmes valeurs que le code d’état HTTP. 

## <a name="next-steps"></a>Étapes suivantes

Voir la [référence de l’API REST Service Bus](https://msdn.microsoft.com/library/azure/hh780717.aspx) pour plus d’informations sur ce que vous pouvez faire avec ces jetons associations de sécurité.

Pour plus d’informations sur l’authentification Bus des services, voir [Bus de Service d’authentification et autorisation](service-bus-authentication-and-authorization.md). 

Autres exemples de sa en c# et JavaScript sont dans [ce billet de blog](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Portail Azure]: https://portal.azure.com