<properties 
    pageTitle="Comment utiliser Notification Hubs avec PHP" 
    description="Découvrez comment utiliser Azure Notification Hubs à partir d’un PHP principale." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="php" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/07/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-php"></a>Comment utiliser Hubs de Notification à partir de PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Vous pouvez accéder à toutes les fonctionnalités de Notification Hubs à partir d’un Java/PHP/Ruby principale à l’aide de l’interface de Notification concentrateur reste comme décrit dans la rubrique MSDN [Notification Hubs reste API](http://msdn.microsoft.com/library/dn223264.aspx).

Dans cette rubrique, nous allons vous montrer comment :

* Création d’un client reste pour les fonctionnalités de Notification Hubs dans PHP ;
* Suivez le [didacticiel mise en route Get](notification-hubs-ios-apple-push-notification-apns-get-started.md) pour votre plateforme mobile de choix, l’implémentation de la partie principale dans PHP.

## <a name="client-interface"></a>Interface client
L’interface client principale peut fournir les mêmes méthodes sont disponibles dans le [Kit de développement .NET Notification Hubs](http://msdn.microsoft.com/library/jj933431.aspx), cela vous permettra à traduire directement les didacticiels et les exemples actuellement disponibles sur ce site et de contribution de la Communauté sur internet.

Vous pouvez trouver le code disponible dans l' [exemple d’emballage reste PHP].

Par exemple, pour créer un client :

    $hub = new NotificationHub("connection string", "hubname"); 

Pour envoyer un iOS native notification :
    
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Mise en œuvre
Si vous n’avez pas encore, suivez notre [didacticiel mise en route Get] jusqu'à la dernière section où vous disposez pour mettre en œuvre la principale.
En outre, si vous souhaitez que vous pouvez utiliser le code de l' [exemple d’emballage reste PHP] et accédez directement à la section [achevé le didacticiel](#complete-tutorial) .

Vous trouverez tous les détails pour implémenter un emballage reste total sur [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Dans cette section, nous décrira l’implémentation PHP des principales étapes requises pour accéder aux points de terminaison REST Hubs de Notification :

1. Analyser la chaîne de connexion
2. Générer le jeton d’autorisation
3. Effectuer l’appel HTTP

### <a name="parse-the-connection-string"></a>Analyser la chaîne de connexion

Voici la classe principale implémentation du client, dont le constructeur qui analyse la chaîne de connexion :

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";
    
        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;
    
        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;
    
            $this->parseConnectionString($connectionString);
        }
    
        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }
    
            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Créer du jeton de sécurité
Les détails de la création de jetons de sécurité sont disponibles [ici](http://msdn.microsoft.com/library/dn495627.aspx).
La méthode suivante doit être ajoutée à la classe **NotificationHub** pour créer le jeton en fonction de l’URI de la requête en cours et les informations d’identification extraites de la chaîne de connexion.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Envoyer une notification
Tout d’abord, nous définir une classe représentant une notification.

    class Notification {
        public $format;
        public $payload;
    
        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;
    
        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }
    
            $this->format = $format;
            $this->payload = $payload;
        }
    }

Ce cours est un conteneur pour un corps de la notification native, ou un ensemble de propriétés dans le cas d’une notification de modèle et un ensemble d’en-têtes qui contient le format (plateforme native ou modèle) et des propriétés spécifiques à la plateforme (par exemple, les propriétés d’expiration de Apple et en-têtes WNS).

Reportez-vous à la [documentation de Notification Hubs reste API](http://msdn.microsoft.com/library/dn495827.aspx) et les formats des plateformes notification spécifique pour toutes les options disponibles.

Grâce à ce cours, nous pouvons maintenant écrire l’envoyer à l’intérieur de la classe **NotificationHub** les méthodes de notification.

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

Les méthodes ci-dessus envoient une demande HTTP POST au point de terminaison /messages de votre concentrateur de notification, avec le corps correcte et les en-têtes d’envoyer la notification.

##<a name="complete-tutorial"></a>Utiliser le didacticiel
Vous pouvez maintenant terminer le didacticiel prise en main en envoyant la notification à partir d’un principale PHP.

Initialisation votre client Notification Hubs (substitue le nom de chaîne et concentrateur de connexion comme indiqué dans le [didacticiel mise en route Get]) :

    $hub = new NotificationHub("connection string", "hubname"); 

Ajoutez ensuite le code envoyer selon votre plateforme mobile cible.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store et Windows Phone 8.1 (non Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 et 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire d’Amazon
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Exécuter votre code PHP doit produire maintenant une notification qui apparaissent sur votre appareil cible.


## <a name="next-steps"></a>Étapes suivantes
Dans cette rubrique nous a montré comment créer un client Java reste simple pour Hubs de Notification. À partir de là, vous pouvez :

* Télécharger les complet d' [exemples d’emballage reste PHP], qui contient le code ci-dessus.
* Actualisez régulièrement vos connaissances sur la Notification Hubs fonctionnalité de marquage du [didacticiel transmettre les nouvelles]
* Découvrez d’émission des notifications à des utilisateurs individuels dans [didacticiel avertir les utilisateurs]

Pour plus d’informations, voir également le [Centre de développement PHP](/develop/php/).

[Exemple d’emballage reste PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Didacticiel mise en route Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 
