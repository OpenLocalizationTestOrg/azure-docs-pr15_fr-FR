<properties
 pageTitle="Guide du développeur - contrôler l’accès à un concentrateur IoT | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - comment contrôler l’accès à un concentrateur IoT et gérer la sécurité"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="control-access-to-iot-hub"></a>Contrôler l’accès à IoT concentrateur

## <a name="overview"></a>Vue d’ensemble

Cet article décrit les options pour sécuriser votre concentrateur IoT. Concentrateur IoT utilise des *autorisations* pour accorder l’accès à chaque extrémité concentrateur IoT. Autorisations de limitent l’accès à un concentrateur IoT basé sur la fonctionnalité.

Cet article décrit :

- Les différentes autorisations que vous pouvez accorder une application appareil ou principale pour accéder à votre plateforme IoT.
- Le processus d’authentification et les jetons qu’il utilise pour vérifier les autorisations.
- Comment les informations d’identification de l’étendue pour limiter l’accès à des ressources spécifiques.
- Prise en charge IoT concentrateur de certificats X.509.
- Mécanismes de d’authentification de périphérique personnalisés qui utilisent existant bureaux d’ordre identité appareil ou schémas d’authentification.

### <a name="when-to-use"></a>Quand utiliser

Vous devez disposer des autorisations appropriées pour accéder à l’un points de terminaison IoT concentrateur. Par exemple, un appareil doit inclure un jeton contenant les informations d’identification de sécurité ainsi que tous les messages qu’il envoie à IoT concentrateur.

## <a name="access-control-and-permissions"></a>Autorisations et contrôle d’accès

Vous pouvez accorder des [autorisations](#iot-hub-permissions) des façons suivantes :

* **Au niveau du concentrateur partagé les stratégies d’accès**. Les stratégies d’accès partagé peuvent accorder n’importe quelle combinaison des [autorisations](#iot-hub-permissions). Vous pouvez définir des stratégies dans le [portail Azure][lnk-management-portal], ou par programme en utilisant le [fournisseur de ressources IoT concentrateur API REST][lnk-resource-provider-apis]. Un concentrateur IoT nouvellement créé dispose des stratégies par défaut suivantes :

    - **iothubowner**: stratégie avec toutes les autorisations.
    - **service**: stratégie avec des autorisations ServiceConnect.
    - **appareil**: stratégie avec des autorisations DeviceConnect.
    - **registryRead**: stratégie avec des autorisations RegistryRead.
    - **registryReadWrite**: stratégie avec des autorisations RegistryRead et RegistryWrite.


* **Informations d’identification de sécurité par périphérique**. Chaque concentrateur IoT contient un [Registre d’identité appareil][lnk-identity-registry]. Pour chaque périphérique dans cette clé de Registre, vous pouvez configurer les informations d’identification de sécurité accorder des autorisations **DeviceConnect** portées aux points de terminaison appareil correspondant.

Par exemple, dans une solution IoT standard :

- Le composant de gestion des périphériques utilise la stratégie *registryReadWrite* .
- Le composant de traitement événement utilise la stratégie de *service* .
- Le composant de la logique runtime appareil entreprise utilise la stratégie de *service* .
- Périphériques individuels se connectent à l’aide des informations d’identification stockées dans identité Registre du hub IoT.

## <a name="authentication"></a>Authentification

Concentrateur IoT Azure accorde l’accès aux points de terminaison en vérifiant un jeton contre les stratégies d’accès partagé et les informations d’identification de périphérique identité Registre sécurité.

Informations d’identification de sécurité, telles que les clés symétriques ne sont jamais envoyées sur le réseau.

> [AZURE.NOTE] Le fournisseur de ressources Azure IoT concentrateur est sécurisé via votre abonnement Azure, sont tous les fournisseurs dans le [Gestionnaire de ressources Azure][lnk-azure-resource-manager].

Pour plus d’informations sur la façon de créer et utiliser des jetons de sécurité, voir [jetons de sécurité IoT concentrateur][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Caractéristiques de protocole

Chaque protocole pris en charge, tels que MQTT AMQP et HTTP, transports jetons de différentes manières.

Lorsque vous utilisez MQTT, le paquet se connecter comporte l’ID de périphérique en tant que l’identifiant du client, {iothubhostname} / {ID de périphérique} dans le champ nom d’utilisateur et un jeton associations de sécurité dans le champ mot de passe. {iothubhostname} doit être l’enregistrement CName complète du concentrateur IoT (par exemple, contoso.azure-devices.net).

Lorsque vous utilisez [AMQP][lnk-amqp], IoT concentrateur prend en charge [SASL brut] [ lnk-sasl-plain] et [AMQP revendications-sécurité basée sur -][lnk-cbs].

Si vous utilisez AMQP revendications--sécurité, la norme spécifie comment transmettre ces jetons.

Pour SASL brut, **nom d’utilisateur** peut être :

* `{policyName}@sas.root.{iothubName}`Si vous utilisez jetons au niveau du concentrateur.
* `{deviceId}@sas.{iothubname}`Si vous utilisez jetons à portée d’appareil.

Dans les deux cas, le champ mot de passe contient le jeton, comme décrit dans les [jetons de sécurité IoT concentrateur][lnk-sas-tokens].

HTTP met en œuvre l’authentification en incluant un jeton valide dans l’en-tête de demande **d’autorisation** .

#### <a name="example"></a>Exemple

Nom d’utilisateur (ID de périphérique respecte la casse) :`iothubname.azure-devices.net/DeviceId`

Mot de passe (générer les associations de sécurité avec l’Explorateur appareil) :`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] La [Azure IoT concentrateur SDK] [ lnk-sdks] générer automatiquement des jetons lors de la connexion au service. Dans certains cas, le SDK ne pas prennent en charge tous les protocoles ou toutes les méthodes d’authentification.

### <a name="special-considerations-for-sasl-plain"></a>Considérations relatives à SASL brut

Lorsque vous utilisez SASL brut avec AMQP, un client qui se connecte à un concentrateur IoT peut utiliser un jeton unique pour chaque connexion TCP. Lorsque le jeton arrive à expiration, la connexion TCP déconnecté du service et déclenche une reconnexion. Ce comportement, bien que pas problématique pour un composant d’application principale, est très dangereux pour une application côté périphérique pour les raisons suivantes :

*  Passerelles se connectent au nom de grand nombre d’appareils. Lorsque vous utilisez brut SASL, ils doivent créer une connexion TCP distincte pour chaque périphérique qui se connecte à un concentrateur IoT. Ce scénario considérablement augmente la consommation de puissance et de ressources de mise en réseau et augmente la latence de chaque connexion de l’appareil.
* Périphériques à ressources limitées sont affectées par l’utilisation des ressources se reconnecter après chaque d’expiration du jeton accrue.

## <a name="scope-hub-level-credentials"></a>Informations d’identification de l’étendue au niveau du concentrateur

Vous pouvez limiter l’étendue des stratégies de sécurité au niveau du concentrateur en créant des jetons avec une ressource restreinte URI. Par exemple, le point de terminaison pour envoyer des messages appareil-nuage à partir d’un appareil est **/devices/ {ID de périphérique} / messages/événements**. Vous pouvez également utiliser une stratégie d’accès partagé au niveau du concentrateur avec des autorisations **DeviceConnect** pour signer un jeton dont ResourceURI des services Web sont **/devices/ {ID de périphérique}**. Cette approche crée un jeton qui n’est utilisable pour envoyer des messages au nom d’appareil **ID de périphérique**.

Ce mécanisme est similaire à la [stratégie de l’éditeur événement Hubs][lnk-event-hubs-publisher-policy]et vous permet de mettre en œuvre des méthodes d’authentification personnalisé.

## <a name="security-tokens"></a>Jetons de sécurité

Concentrateur IoT utilise des jetons de sécurité pour authentifier périphériques et services afin d’éviter l’envoi de clés sur le réseau. En outre, les jetons de sécurité sont limités dans l’étendue et de la période de validité. [Azure IoT concentrateur SDK] [ lnk-sdks] générer automatiquement des jetons sans configuration spéciale. Certains scénarios, requièrent toutefois, l’utilisateur générer et utiliser des jetons de sécurité directement. Ces incluent l’utilisation directe des surfaces MQTT, AMQP ou HTTP, ou l’implémentation du modèle service jeton, comme indiqué dans [l’authentification personnalisée appareil][lnk-custom-auth].

Concentrateur IoT permet également appareils pour vous authentifier avec IoT concentrateur à l’aide de [certificats X.509][lnk-x509]. 

### <a name="security-token-structure"></a>Structure de jeton de sécurité
Vous utilisez des jetons de sécurité pour accorder l’accès limité à la fois aux périphériques et services à des fonctionnalités spécifiques dans IoT Hub. Pour vous assurer que seuls les périphériques autorisés et les services peuvent se connecter, jetons de sécurité doivent être signés avec une clé de stratégie d’accès partagé ou une clé symétrique stockée avec une identité de périphérique dans le Registre identité.

Un jeton connectés avec un accès partagé stratégie autorise clés accéder à toutes les fonctionnalités associées aux autorisations stratégie accès partagé. En revanche, un jeton signé avec symétrique clé d’une identité périphérique accorde uniquement l’autorisation **DeviceConnect** pour l’identité du périphérique associé.

Le jeton de sécurité a le format suivant :

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Voici les valeurs prévues :

| Valeur | Description |
| ----- | ----------- |
| {signature} | Une chaîne de signature HMAC SHA256 du formulaire : `{URL-encoded-resourceURI} + "\n" + expiry`. **Important**: la clé est en base 64 de décoder et permettant d’effectuer le calcul HMAC SHA256 comme clé. |
| {ResourceURI des services Web} | Préfixe URI (par segment) des points de terminaison accessible avec ce jeton, en commençant par le nom d’hôte du concentrateur IoT (aucune protocol). Par exemple,`myHub.azure-devices.net/devices/device1` |
| {expiration} | Chaînes UTF8 pour le nombre de secondes depuis l’UTC 00:00:00 version 1er janvier 1970. |
| {URL-codé-ResourceURI des services Web} | Inférieur cas codage URL de la ressource de minuscules URI |
| {Stratégie} | Le nom de la stratégie d’accès partagé à laquelle ce jeton fait référence. Absent dans le cas des jetons faisant référence à des informations d’identification du Registre de l’appareil. |

**Remarque sur préfixe**: préfixe URI est calculée par segment et non par caractère. Par exemple `/a/b` est un préfixe `/a/b/c` mais pas pour les `/a/bc`.

L’extrait Node.js suivant montre une fonction appelée **generateSasToken** qui calcule le jeton des entrées `resourceUri, signingKey, policyName, expiresInMins`. Les sections suivantes expliquent comment initialisation les entrées différentes pour les exemples d’utilisation jeton différents.

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

En tant qu’une comparaison, le code Python équivalent pour générer un jeton de sécurité est :

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] Étant donné que la validité du jeton est validée sur les ordinateurs IoT concentrateur, il est important que la dérive de l’horloge de l’ordinateur qui génère le jeton être minimale.

### <a name="use-sas-tokens-in-a-device-client"></a>Utiliser les associations de sécurité jetons dans un client d’appareil

Il existe deux façons d’obtenir des autorisations **DeviceConnect** avec concentrateur IoT avec des jetons de sécurité : utiliser une [clé de périphérique symétrique à partir du Registre identité appareil](#use-a-symmetric-key-in-the-identity-registry), ou utiliser une [clé de stratégie d’accès partagé](#use-a-shared-access-policy).

N’oubliez pas que toutes les fonctionnalités accessibles à partir d’appareils sont exposée par la conception sur les points de terminaison avec préfixe `/devices/{deviceId}`.

> [AZURE.IMPORTANT] Le seul moyen que IoT concentrateur authentifie un périphérique spécifique à l’aide de la clé symétrique d’identité appareil. Dans les cas lorsqu’une stratégie d’accès partagé est utilisée pour accéder aux fonctionnalités de l’appareil, la solution doit prendre en compte le composant émission du jeton de sécurité comme un composant approuvé.

Les points de terminaison not appareil sont (quelle que soit le protocole) :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Envoyer des messages de l’appareil-nuage. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Recevoir des messages de cloud vers le périphérique. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utiliser une clé symétrique dans le Registre d’identité

Lorsque vous utilisez clé symétrique d’une identité appareil pour générer un jeton de la stratégie (`skn`) élément du jeton est omis.

Par exemple, un jeton créé pour accéder à toutes les fonctionnalités de l’appareil doit avoir les paramètres suivants :

* URI de la ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* la clé de signature : n’importe quelle touche symétrique pour la `{device id}` identité,
* Aucun nom de la stratégie
* un délai d’expiration.

Un exemple d’utilisation de la fonction nœud ci-dessus serait le suivant :

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

Le résultat, qui accorde l’accès à toutes les fonctionnalités d’appareil1, serait le suivant :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] Il est possible de générer un jeton de sécurité à l’aide de l’outil .NET [Explorateur de périphérique][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Utiliser une stratégie d’accès partagé

Lorsque vous créez un jeton à partir d’une stratégie d’accès partagé, la stratégie de nom de champ `skn` doit être définie sur le nom de la stratégie utilisée. Il est également obligatoire que la stratégie accorde l’autorisation **DeviceConnect** .

Les deux principaux scénarios d’utilisation des stratégies d’accès partagé pour accéder aux fonctionnalités de l’appareil sont :

* [nuage de passerelles protocole][lnk-endpoints],
* [services des jetons] [ lnk-custom-auth] utilisée pour implémenter des schémas d’authentification personnalisés.

Étant donné que la stratégie d’accès partagé potentiellement accorder l’accès à se connecter en tant que n’importe quel appareil, il est important d’utiliser la bonne ressource URI lors de la création des jetons de sécurité. Ceci est particulièrement important pour les services jetons, ayant pour définir l’étendue du jeton à un périphérique spécifique à l’aide de la ressource URI. Ce point est moins pertinent pour passerelles protocole qu’ils sont déjà médiation le trafic de tous les périphériques.

Par exemple, un service de jetons à l’aide de la stratégie d’accès partagé prédéfinis appelée **appareil** créez un jeton avec les paramètres suivants :

* URI de la ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* la clé de signature : une des clés de la `device` stratégie,
* nom de la stratégie : `device`,
* un délai d’expiration.

Un exemple d’utilisation de la fonction nœud ci-dessus serait le suivant :

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Le résultat, qui accorde l’accès à toutes les fonctionnalités d’appareil1, serait le suivant :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Une passerelle de protocole peut utiliser le même jeton pour tous les périphériques, il suffit d’affecter la ressource URI à `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Utiliser les jetons de sécurité à partir des composants de service

Composants du service peuvent générer uniquement des jetons de sécurité à l’aide de stratégies d’accès partagé octroyer des autorisations appropriées comme expliqué précédemment.

Ce sont les fonctions de service exposées sur les points de terminaison :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices` | Créer, mettre à jour, récupérer et supprimer des identités de l’appareil. |
| `{iot hub host name}/messages/events` | Recevoir des messages de l’appareil-nuage. |
| `{iot hub host name}/servicebound/feedback` | Recevoir des commentaires pour les messages de cloud vers le périphérique. |
| `{iot hub host name}/devicebound` | Envoyer des messages de cloud vers le périphérique. |

Par exemple, un service génération à l’aide de la stratégie d’accès partagé prédéfinis appelée **registryRead** créez un jeton avec les paramètres suivants :

* URI de la ressource : `{IoT hub name}.azure-devices.net/devices`,
* la clé de signature : une des clés de la `registryRead` stratégie,
* nom de la stratégie : `registryRead`,
* un délai d’expiration.

    point de terminaison var = « myhub.azure-devices.net/devices » ;   Stratégie var = 'unité' ;   var policyKey = '...' ;

    var jeton = generateSasToken (point de terminaison, policyKey, stratégie, 60) ;

Le résultat, qui serait accorder l’accès pour lire toutes les identités d’appareil, serait le suivant :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Certificats X.509 pris en charge

Vous pouvez utiliser n’importe quel certificat X.509 pour vous authentifier un appareil avec IoT concentrateur. Cela inclut :

-   **Un certificat X.509 existant**. Un périphérique peut avoir déjà un certificat X.509 associé. Le périphérique peut utiliser ce certificat pour vous authentifier avec IoT concentrateur.

-   **Un certificat X-509 généré automatiquement et auto-signé**. Un responsable du déploiement en interne ou du fabricant appareil peut générer ces certificats et stocker la clé privée correspondante (et le certificat) sur l’appareil. Vous pouvez utiliser les outils tels que [OpenSSL] [ lnk-openssl] et [Windows SelfSignedCertificate] [ lnk-selfsigned] utilitaire à cet effet.

-   **Certificat X.509 signé autorité de certification**. Vous pouvez également utiliser un certificat X.509 généré et signé par une autorité de Certification (CA) pour identifier un appareil et d’authentifier un appareil avec IoT concentrateur.

Un périphérique peut utiliser un certificat X.509 ou un jeton de sécurité pour l’authentification, mais pas les deux.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Enregistrer un certificat client X.509 pour un appareil

Le [Kit de développement de Azure IoT Service pour c#] [ lnk-service-sdk] (version 1.0.8+) prend en charge l’enregistrement d’un dispositif qui utilise un certificat client X.509 pour l’authentification. Autres API comme importer/exporter des appareils prennent également en charge les certificats clients X.509.

### <a name="c-support"></a>C\# prise en charge

La classe **RegistryManager** fournit un moyen par programme inscrire un appareil. En particulier, les méthodes **AddDeviceAsync** et **UpdateDeviceAsync** permettent à un utilisateur inscrire et mettre à jour un périphérique dans le Registre Iot concentrateur appareil identité. Ces deux méthodes prennent une instance de **périphérique** comme entrée. La classe **appareil** inclut une propriété **d’authentification** qui permet à l’utilisateur spécifier les empreintes de certificat X.509 principales et secondaires. L’empreinte numérique représente un hachage ça-1 du certificat X.509 (stocké en utilisant le codage DER binaire). Les utilisateurs ont la possibilité de spécifier une empreinte principale ou une empreinte secondaire ou les deux. Empreintes principaux et secondaires sont pris en charge afin de gérer des scénarios de survol certificat.

> [AZURE.NOTE] Concentrateur IoT ne nécessitent ni stocker le certificat client X.509 tout entier, uniquement l’empreinte numérique.

Voici un exemple C\# extrait de code pour enregistrer un périphérique utilisant un certificat client X.509 :

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Utiliser un certificat client X.509 pendant les opérations de runtime

Le [périphérique IoT Azure SDK pour .NET] [ lnk-client-sdk] (version 1.0.11+) prend en charge l’utilisation de certificats clients X.509.

### <a name="c-support"></a>C\# prise en charge

La classe **DeviceAuthenticationWithX509Certificate** prend en charge la création d’instances de  **DeviceClient** à l’aide d’un certificat client X.509.

Voici un extrait de code exemple :

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Authentification de périphérique personnalisé

Vous pouvez utiliser le IoT Hub [Registre identité de périphérique] [ lnk-identity-registry] pour configurer les informations d’identification de sécurité par périphérique et accéder à l’aide des [jetons]de contrôle[lnk-sas-tokens]. Toutefois, si une solution IoT comporte déjà un investissement significatif dans un jeu personnalisé appareil identité Registre et/ou l’authentification, vous pouvez intégrer cette infrastructure existante avec concentrateur IoT en créant un *service de jeton*. De cette façon, vous pouvez utiliser d’autres fonctionnalités IoT dans votre solution.

Un service de jetons est un service cloud personnalisé. Il utilise un concentrateur IoT *stratégie d’accès partagé* avec des autorisations **DeviceConnect** pour créer des jetons *à portée d’appareil* . Ces jetons activer un appareil pour vous connecter à votre plateforme IoT.

  ![Étapes du modèle de service de jetons][img-tokenservice]

Voici les principales étapes du modèle de service de jeton :

1. Créer une stratégie d’accès concentrateur IoT partagé avec des autorisations de **DeviceConnect** pour votre plateforme IoT. Vous pouvez créer cette stratégie dans le [portail Azure] [ lnk-management-portal] ou par programme. Le service de jeton utilise cette stratégie pour signer les jetons qu’il crée.
2. Lorsqu’un appareil a besoin accéder à votre plateforme IoT, il demande un jeton signé de votre service de jeton. L’appareil peut vous authentifier avec votre modèle de Registre / d’authentification d’identité périphérique personnalisés pour identifier le périphérique que le service de jeton utilise pour créer le jeton.
3. Le service de jeton retourne un jeton. Le jeton est créé à l’aide de `/devices/{deviceId}` en tant que `resourceURI`, avec `deviceId` comme périphérique en cours d’authentification. Le service de jeton utilise la stratégie d’accès partagé pour construire le jeton.
4. Le périphérique utilise le jeton directement avec le hub IoT.

> [AZURE.NOTE] Vous pouvez utiliser la classe .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] ou la classe Java [IotHubServiceSasToken] [ lnk-java-sas] pour créer un jeton dans votre service de jeton.

Le service de jeton pouvez définir la date d’expiration jeton comme vous le souhaitez. Lorsque le jeton arrive à expiration, le hub IoT serveurs la connexion du périphérique. Ensuite, le périphérique doit demander un nouveau jeton à partir du service jeton. Si vous utilisez un court délai, cela permet d’augmenter la charge sur l’appareil et le service de jeton.

Pour un appareil pour vous connecter à votre concentrateur, vous devez l’ajouter toujours dans le Registre IoT concentrateur appareil identité, même si le périphérique utilise un jeton et pas une clé de l’appareil pour vous connecter. Par conséquent, vous pouvez continuer à utiliser le contrôle d’accès par périphérique en activant ou désactivant les identités des appareils dans le [Registre d’identité IoT concentrateur] [ lnk-identity-registry] lorsque l’appareil s’authentifie avec un jeton. Cela permet d’atténuer les risques liés à l’aide des jetons avec délais d’expiration de temps.

### <a name="comparison-with-a-custom-gateway"></a>Comparaison avec une passerelle personnalisée

Le modèle de service de jeton est recommandée pour implémenter un schéma de Registre/authentification identité personnalisée avec IoT concentrateur. Il est recommandé, car IoT concentrateur continue gérer la plupart du trafic solution. Toutefois, il existe des cas où le schéma d’authentification personnalisé est donc étroitement avec le protocole qu’un service de traitement de tout le trafic (*passerelle personnalisées*) est requis. Un exemple est [communiquées touches (PSKs) et sécurité TLS (Transport Layer)][lnk-tls-psk]. Pour plus d’informations, voir la [passerelle protocole] [ lnk-protocols] rubrique.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence vous fournissent plus d’informations sur le contrôle de l’accès à votre concentrateur IoT.

## <a name="iot-hub-permissions"></a>Autorisations IoT concentrateur

Le tableau suivant répertorie les autorisations que vous pouvez utiliser pour contrôler l’accès à votre plateforme IoT.

| Autorisation            | Notes |
| --------------------- | ----- |
| **RegistryRead**      | Accorde l’accès au Registre identité appareil en lecture. Pour plus d’informations, consultez [appareil identité Registre][lnk-identity-registry]. |
| **RegistryReadWrite** | Aides lire et écrivent access dans le Registre identité appareil. Pour plus d’informations, consultez [appareil identité Registre][lnk-identity-registry]. |
| **ServiceConnect**    | Accorde l’accès au cloud service not communications et analyse des points de terminaison. Par exemple, il accorde l’autorisation aux services cloud principale pour recevoir des messages de l’appareil-nuage, envoyer des messages de cloud vers le périphérique et récupérer les accusés de remise correspondante. |
| **DeviceConnect**     | Accorde l’accès aux points de terminaison de communication not appareil. Par exemple, il accorde l’autorisation d’envoyer des messages de l’appareil-nuage et recevoir des messages de cloud vers le périphérique. Cette autorisation est utilisée par les périphériques. |

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

Autres rubriques de référence dans le Guide du développeur sont les suivantes :

- [Points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion.
- [Limitation et les quotas de] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT concentrateur et le comportement de limitation s’attendre lorsque vous utilisez le service.
- [Concentrateur IoT périphérique et service SDK] [ lnk-sdks] répertorie le langage diverses SDK vous une utilisation lorsque vous développez périphérique et service applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, méthodes et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations à partir du IoT Hub sur votre jumeaux appareil, les méthodes et les tâches.
- [Prise en charge IoT concentrateur MQTT] [ lnk-devguide-mqtt] donne des informations supplémentaires sur la prise en charge IoT concentrateur pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à contrôler l’accès IoT concentrateur, il est possible que vous intéresser dans les rubriques suivantes Guide du développeur :

- [Jumeaux de périphérique permet de synchroniser les configurations et état][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs appareils][lnk-devguide-jobs]

Si vous voulez essayer certaines des concepts décrits dans cet article, il est possible que vous intéresser les didacticiels IoT concentrateur suivants :

- [Prise en main Azure IoT concentrateur][lnk-getstarted-tutorial]
- [Comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT][lnk-c2d-tutorial]
- [Comment traiter les messages d’appareil-nuage IoT concentrateur][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
